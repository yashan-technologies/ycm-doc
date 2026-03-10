Except for special instructions, the following steps are all performed in the environment where the yasadm tool is located. The paths, users, passwords, ports, and other parameters in the commands should be replaced with actual values.

There are two ways to upgrade the primary/standby management platform in a high-availability deployment:

## **(Recommended) One-click Upgrade of Primary/Standby Management Platform**

This method upgrades all primary/standby management platforms and the managed servers. To use this upgrade method, all servers where the backup management platforms are located must be added to the management platform. When upgrading the server ycm-agent, the backup management platforms on the server will also be upgraded.

> **Note**：
>
> Starting from version 23.2.10.100, when deploying the primary/standby management platform, the server will be added to the management platform by default. In previous versions, it must be added manually to the management platform.

This upgrade step needs to be executed on the server where the primary management platform is deployed, and the rest of the steps are the same as for a single-node management platform upgrade. Please refer to [Single-Node Offline Upgrade](Single-Node Offline Upgrade) for details.

## **Upgrade the Primary Management Platform First, Then the Backup Management Platform in Sequence**

This method requires multiple upgrade commands. First, upgrade the primary management platform and all managed servers, then sequentially upgrade the local backup management platforms. Please refer to the following steps.

This document takes an upgrade from version 23.2.6.100 to 23.2.7.100 as an example.

### Upgrade the Primary Management Platform

> **Note**：
>
> To prevent a primary/standby switch during the upgrade, please stop all backup management platforms (if there are multiple backup management platforms, please execute the command multiple times in sequence).
> 
> On the server where the backup management platform is deployed, stop the backup management platform: `/{Backup Management Platform Installation Path}/ycm/monit/monitctl run stop -g ycm`

#### SSH Upgrade Method (Recommended)

##### Step 1: Upload the New Version Installation Package

1. On the server where the management platform is deployed, create an empty directory, which will serve as the temporary storage location for the upgrade installation package. Note that this directory should not be placed under the installation directory of the management platform.

  ```shell
$ mkdir ~/upgrade
  ```

2. Upload the new version installation package to this directory.

3. Run the following command to extract the installation package.

  ```shell
$ cd ~/upgrade
$ tar -zxf yashandb-cloud-manager-23.2.7.100-linux-x86_64.tar.gz
  ```

##### Step 2: Generate Configuration File

This step must be executed in the temporary storage directory of the upgrade installation package.

1. Run the following command to enter the temporary storage directory of the upgrade installation package:

```bash
$ cd ~/upgrade/ycm
```

2. Execute the following command to generate the configuration file for host information.

  ```bash
# Generate ycm_host.toml in the current directory
$ ./scripts/yasadm host gen -u yashan -p password -i /opt/ycm --port 22
  ```

   Brief parameter introduction:

  ```bash
-u, --username    SSH user of the platform server
-p, --password    SSH password of the platform server user
-i, --install-dir Installation path of the platform
--port            SSH port of the platform server
  ```

3. Modify the configuration file information according to actual requirements.

  ```bash
$ vim ycm_host.toml
...
[[hosts]]
  host_id = "host-5e99117d"
  manage_ip = "192.168.1.2"
  agent_user = "yashan"
  port = 22
  ssh_user = "yashan"
  ssh_pwd = "123456"
  ...
[[hosts]]
 ...
  ```

##### Step 3: Execute Offline Upgrade

> **Caution**：
>
> If the upgrade fails, re-upgrade is not supported, only [rollback](#back) is allowed.

This step must be executed in the temporary storage directory of the upgrade installation package.

1. Run the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the upgrade command.

```bash
# Upgrade via ssh

$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yasdb_home --role primary
```

Brief parameter introduction:

```bash
-i, --install-dir     Installation path of the management platform
-t, --toml            Configuration file of the managed server information
--imp-exp-install-dir Path where theYashanDB client with imp and exp is installed, used to back up the data of the backend YashanDB of the platform
--role primary        Upgrade the primary management platform
```

##### Step 4: Check the Version After Upgrade

Open a browser to access and log in to the management platform, click on the user avatar in the upper right corner, click **[ About ]**, and check the version after the upgrade.

#### RPC Upgrade Method

##### Step 1: Upload the New Version Installation Package

1. On the server where the management platform is deployed, create an empty directory, which will serve as the temporary storage location for the upgrade installation package.

  ```shell
$ mkdir ~/upgrade
  ```

2. Upload the new version installation package to this directory.

3. Run the following command to extract the installation package.

  ```shell
$ cd ~/upgrade
$ tar -zxf yashandb-cloud-manager-23.2.7.100-linux-x86_64.tar.gz
  ```

##### Step 2: Generate Configuration File

This step must be executed in the temporary storage directory of the upgrade installation package.

1. Run the following command to enter the temporary storage directory of the upgrade installation package:

```bash
$ cd ~/upgrade/ycm
```

2. Execute the following command to generate the configuration file for the managed servers.

  ```bash
# Generate ycm_host.toml in the current directory
$ ./scripts/yasadm host gen -i /opt/ycm --rpc-port 9072
  ```

   Brief parameter introduction:

  ```bash
-i, --install-dir   Installation path of the management platform
--rpc-port          Upgrade RPC port of the management platform server, default is 9072
  ```

3. Modify the configuration file information according to actual requirements.

  ```bash
$ vim ycm_host.toml
...
[[hosts]]
  host_id = "host-5e99117d"
  ...
  upgrade_rpc_port = 9072
[[hosts]]
 ...
  ```

##### Step 3: Execute Offline Upgrade

> **Caution**：
>
> If the upgrade fails, re-upgrade is not supported, only [rollback](#back) is allowed.

This step must be executed in the temporary storage directory of the upgrade installation package.

1. Run the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the upgrade command.

```bash
# Upgrade via rpc

$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yasdb_home --rpc --role primary
```

Brief parameter introduction:

```bash
-i, --install-dir     Installation path of the management platform
-t, --toml            Configuration file of the managed server information
--rpc                 Upgrade via RPC, default is SSH method
--imp-exp-install-dir Path where the YashanDB client with imp and exp is installed, used to back up the data of the backend YashanDB of the platform
--role primary        Upgrade the primary management platform
```

##### Step 4: Check the Version After Upgrade

Open a browser to access and log in to the management platform, click on the user avatar in the upper right corner, click **[ About ]**, and check the version after the upgrade.

<span id="back" name="back" class="yaslink"></span>

### Upgrade the Backup Management Platform

This step needs to be executed on the server where the backup management platform is deployed.

> **Note**：
>
> The upgrade package for the backup management platform must have the same version as the upgrade package for the primary management platform.

#### Step 1: Upload the New Version Installation Package

1. On the server where the management platform is deployed, create an empty directory, which will serve as the temporary storage location for the upgrade installation package.

  ```shell
$ mkdir ~/upgrade
  ```

2. Upload the new version installation package to this directory.

3. Run the following command to extract the installation package.

  ```shell
$ cd ~/upgrade
$ tar -zxf yashandb-cloud-manager-23.2.7.100-linux-x86_64.tar.gz
  ```

#### Step 2: Execute Offline Upgrade

> **Caution**：
>
> If the upgrade fails, re-upgrade is not supported, only [rollback](#back) is allowed.

This step must be executed in the temporary storage directory of the upgrade installation package.

1. Run the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the upgrade command.

```bash
$ ./scripts/yasadm upgrade -i /opt/ycm --lib-dir /home/yashan/yasdb_home/lib --role standby
```

Brief parameter introduction:

```bash
-i, --install-dir     Installation path of the management platform
--lib-dir             lib path for YashanDB client
--role standby        Upgrade the backup management platform
```

#### Step 3: Check the Version After Upgrade

Enter the installation directory of the backup management platform and check the version after the upgrade through the `version.toml` configuration file.

```shell
# Enter the installation directory of the backup management platform
$ cd /{Backup Management Platform Installation Directory}/ycm

# Open version.toml file to check the upgraded version
$ cat etc/upgrade/version.toml
...
version = "23.2.7.100" # Version after upgrade

[[upgrade]]
source_version = ["23.2.6.100"]
path = "23.2.6.100"
...
```

### Upgrade Rollback

> **Note**：
>
> Rollback is not supported after the upgrade is successful; rollback is only allowed when the upgrade fails.

The upgrade process of the management platform may fail due to command errors, configuration file errors, version errors, etc. In this case, please run the rollback to restore the environment before the upgrade.

1. Run the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the rollback command.

```bash
# Rollback for primary management platform
$ ./scripts/yasadm rollback -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yasdb_home

# Rollback for backup management platform
$ ./scripts/yasadm rollback -i /opt/ycm --lib-dir /home/yashan/yasdb_home/lib
```

Brief parameter introduction:

```bash
-i, --install-dir      Installation path of the management platform
-t, --toml             SSH configuration file for the managed server information
--imp-exp-install-dir  Path where the YashanDB client with imp and exp is installed, used to restore the backend YashanDB data of the platform
--lib-dir              lib path for YashanDB client
```

> **Note**：
>
> - Rollback is allowed to fail and retry.
> - If multiple rollbacks fail, please contact technical support for the management platform for assistance.