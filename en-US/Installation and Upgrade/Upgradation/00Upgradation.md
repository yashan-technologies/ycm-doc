The management platform supports offline upgrades of the management platform and managed hosts via the yasadm tool, replacing the related binary files with the new version.

## Notes

- Before upgrading, you need to back up the management platform and management hosts. For detailed operations, please refer to [Backup and Recovery](../../Platform Management/Platform Operation/Backup and Recovery).
- During the upgrade process, related processes will be restarted, and monitoring data for the servers and managed databases will be temporarily unavailable.
- Please complete all pre-upgrade preparation tasks before running the upgrade.
- After a successful upgrade, first confirm that the management platform is functioning normally before opening user services.
- If the upgrade is unsuccessful, please run the rollback procedure. If the rollback is also unsuccessful, you may try rolling back again. If multiple attempts fail, please contact technical support for resolution.
- The yasadm tool from the new upgrade package must be used during the upgrade process. For meanings and descriptions of the various command options of this tool, please refer to [yasadm](../../Reference/Tools Introdution/yasadm).
- Do not extract the upgrade installation package to the management platform's installation directory to execute the upgrade command.


> **Note**：
>
> When the old version is 23.2.1.105 or earlier, upgrading to version 23.2.2.100 or higher may cause existing alarms to be repaired and trigger a new alarm due to alarm metric effects.
> 
> Starting from version 23.4.10.2, the privilege of the database operation management user YASOM has been downgraded. Upgrading from the old version to the new version will not change the YASOM user's privilege. Users need to manually adjust according to their needs. For minimum privileges assigned to the YASOM user, please refer to [Database Management](../../Platform Management/Resource Management/Database Management).

## Pre-upgrade Preparation 

### Step 1: Obtain the New Version Installation Package

Please confirm with technical support whether the currently used management platform version can be directly upgraded to the new version. Currently, it is supported to upgrade from version **23.1.2.100**.

### Step 2: Confirm Installation Directory Space

Offline upgrades will create a temporary directory ($HOME/ycm_{old version number}_{ SCN }, $HOME/ycm-agent_{old version number}_{ SCN }) as a backup directory, which will contain backup files for the current management platform and ycm-agent. Please ensure that the user performing the upgrade has sufficient storage space, with a minimum of 4G remaining disk space by default.

### Step 3: Confirm Upgrade Method

The management platform supports two different upgrade methods: SSH and rpc, for upgrading the servers managed by the management platform:

- **SSH Upgrade Method (Recommended)**: This is the default upgrade method, which performs the upgrade operation via SSH connection to the server. This method requires providing the server's SSH user and password.

- **rpc Upgrade Method**: Before the upgrade, deploy the upgrade agent process on the server and execute the upgrade operation via rpc connection to the upgrade agent. This method requires opening the rpc port needed for the upgrade. After the upgrade is complete, the upgrade agent will be cleaned up. This method can be used if SSH user and password cannot be provided.

The checks for different upgrade methods are as follows:

**SSH Upgrade Method**

When upgrading via SSH, SSH and SFTP services must be used. Please check if the SSH service on each ycm-agent host is enabled and allows the upgrade operation user to log in remotely.

You can check if the SSH service is enabled with the following command:

  ```shell
  $ systemctl status sshd.service
  ● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-04-26 14:32:52 CST; 3 months 6 days ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 6372 (sshd)
     CGroup: /system.slice/sshd.service
             └─6372 /usr/sbin/sshd -D
  ```

You can check whether remote login for the operating user can be performed during the upgrade with the following command:

Log in to the current server using current user credentials via SSH. SSH may use either password or keyless connection:

  ```shell
$ ssh username@ip -p SSH port
  ```

Enter the password as prompted to see if you can successfully connect to the current SSH server.

**rpc Upgrade Method**

When upgrading via rpc, the upgrade port must be used. Please check if the ports used for the upgrade on each ycm-agent server are available. The default upgrade port is 9072. You can check with the following command:

  ```shell
# If the result is Connection refused, it means the port is not occupied and can be used for upgrading
$ telnet ip port    # For example: telnet 192.168.1.2 9072
  
# If telnet is not installed, execute the following commands
$ sudo yum list telnet*              # List telnet-related installation packages
$ sudo yum install telnet-server     # Install telnet service
$ sudo yum install telnet.*          # Install telnet client
  ```

### Step 4: Check Processes

Before upgrading, please ensure that various process statuses of the management platform and ycm-agent hosts are normal. You can check the process status with the following command:

```shell
# Check processes of the management platform
$ sudo /opt/ycm/ycm/monit/monitctl run
Monit 5.29.0 uptime: 20h 35m
┌─────────────────────────────────┬────────────────────────────┬───────────────┐
│ Service Name                    │ Status                     │ Type          │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ AchorBase                       │ OK                         │ System        │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ ycm                             │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ yashandb_exporter               │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ yas-hcs-api                     │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ promtail                        │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ prometheus                      │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ loki                            │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-ycm                        │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-yashandb_exporter          │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-yas-hcs-api                │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-promtail                   │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-prometheus                 │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-loki                       │ OK                         │ Program       │
└─────────────────────────────────┴────────────────────────────┴───────────────┘      

# Check ycm-agent's processes
$ sudo /opt/ycm/ycm-agent/monit/monitctl run
Monit 5.29.0 uptime: 20h 35m
┌─────────────────────────────────┬────────────────────────────┬───────────────┐
│ Service Name                    │ Status                     │ Type          │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ AchorBase                       │ OK                         │ System        │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ ycm-agent                       │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ promtail                        │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ node_exporter                   │ OK                         │ Process       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-ycm-agent                  │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-promtail                   │ OK                         │ Program       │
├─────────────────────────────────┼────────────────────────────┼───────────────┤
│ hold-node_exporter              │ OK                         │ Program       │
└─────────────────────────────────┴────────────────────────────┴───────────────┘
```

### Step 5: Confirm the Type of Backend Database

Please check the DB-related configuration in the `etc/ycm.yaml` file in the installation directory to confirm the type of backend database for the management platform.