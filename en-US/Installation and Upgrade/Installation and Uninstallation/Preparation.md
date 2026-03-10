## Server Preparation

### Operating System Requirements

|Server Type |Operating System |Supported Versions |
| ----------- | -------- | --------------------------------------- |
| x86_64       | CentOS           | 7.9.2009 and above                             |
| x86_64       | Ubuntu           | 20.04.1 and above                              |
| ARM aarch64  | Kylin            | Kylin Linux Advanced Server V10 (Lance)      |

### Server Configuration

- The management platform can share the server with hosted resources, and resource allocation should be considered.
- When deployed independently, different minimum configuration servers should be provided depending on the number of managed databases.

|Hosted Database Scale |Minimum Server Configuration |
| -------------- | ----------------------- |
| 1-2 Instances         | 2 cores, 4G memory, 30G disk      |
| 3-10 Instances        | 4 cores, 8G memory, 50G disk      |
| 11-60 Instances       | 8 cores, 16G memory, 100G disk    |
| More than 60 Instances | 16 cores, 32G memory, 500G disk   |

>**Note**：
>
> Any deployment form of YashanDB, all types of database nodes under this database are considered instances and need to be included in statistics.
>
> The minimum disk configuration does not include space for storing database backups; if backups need to be stored on the management platform host, please increase the disk configuration as appropriate.

### Open Ports

The following ports are used by default; it is also possible to modify the ports during deployment in `etc/deploy.yml`. For detailed port descriptions, please refer to the [Port List](../../Reference/Port List).

|Server |Default Ports |
| ---------------------------------------- | ------------------------------------------------------------------- |
| Management Platform Server                  | 9060, 9061, 9062, 9063, 9064 |
| Database Server or other servers needing platform hosting | 9070 and 9071                                             |

Ensure the ports are available by the following methods:

  Method 1: Disable the firewall.

  ```shell
  # Check firewall status
  # systemctl status firewalld or firewall-cmd --state
  
  # Stop the firewall
  # systemctl stop firewalld 
  
  # Disable at startup
  # systemctl disable firewalld
  ```

  Method 2: Add to whitelist.

  ```shell
  # Open ports 9060 to 9064, 9070, 9071 without turning off the firewall
  # View whitelist (port openness status)
  # firewall-cmd --zone=public --list-ports
  
  # Add open ports (--permanent is for lasting effect; without this parameter, it will not be effective after a reboot)
  # firewall-cmd --zone=public --add-port=9060/tcp --permanent
  # firewall-cmd --zone=public --add-port=9061/tcp --permanent
  # firewall-cmd --zone=public --add-port=9062/tcp --permanent
  # firewall-cmd --zone=public --add-port=9063/tcp --permanent
  # firewall-cmd --zone=public --add-port=9064/tcp --permanent
  # firewall-cmd --zone=public --add-port=9070/tcp --permanent
  # firewall-cmd --zone=public --add-port=9071/tcp --permanent
  
  # After opening ports, restart the firewall
  # systemctl stop firewalld
  # systemctl start firewalld
  
  # To remove already added ports from whitelist, use the following command
  # firewall-cmd --zone=public --remove-port=9071/tcp --permanent
  ```

### Example Server List for Installation

The following lists the server information used for installation examples: (All listed servers are of X86_64 architecture and have installed CentOS version 7.9.2009 or above)

|Server Name |Server IP |Role |
| ---------- | ----------- | ---- |
| Server 1    | 192.168.1.2  | Primary  |
| Server 2    | 192.168.1.3  | Secondary |
| Server 3    | 192.168.1.4  | Secondary |

## User Preparation

Taking the user yashan as an example, all subsequent installation steps are performed under this user after the user creation and authorization.

1. Create User.

```shell
# Create yashan user
# useradd -d /home/yashan -m yashan
# passwd yashan
```

2. Grant sudo privileges.

```shell
# Grant permissions to the user and open the /etc/sudoers file
# cd /etc
# ll sudoers
# chmod +w sudoers
# vi /etc/sudoers

# Add the following content at the end of the file and save and exit
# yashan  ALL=(ALL)NOPASSWD:ALL

# chmod -w sudoers
```

3. Check umask privilege mask.

Ensure the umask privilege mask is 022 or 002.

```shell
# Check current umask privilege mask
# umask

# If the privilege mask is not 022 or 002, add umask 022 in ~/.bashrc
# echo "umask 022" >> ~/.bashrc
```

## Application Environment Preparation

### libnsl Environment Preparation

```bash
# Check if libnsl exists
# ldconfig -p | grep libnsl

# If it does not exist, install using the following method
# yum install libnsl  
# or
# apt update && apt-get -y install rpm  && rpm -ivh libnsl-2.28-164.el8.aarch64.rpm --nodeps 
```

### Database Environment Preparation (Optional)

The backend database can be either sqlite3 or YashanDB. If YashanDB is chosen, it must be deployed in advance for standalone deployment; this YashanDB is used for the normal service operation of the management platform.

|sqlite3 (Default) |Description |
| --------------- | ---------------- |
| Version Requirement | 3.7.17 and above        |

|YashanDB |Description |
| ---------- | ----------------------------------------------------------------------- |
| Version Requirement | 22.2 and above                                         |
| Deployment Type     | Standalone Database                                 |
| Database User       | Create yasman user, grant dba privilege: `create user yasman;grant dba to yasman;` |

### Startup Self-Start Prerequisites

In some versions of operating systems (such as Ubuntu 18.04 and higher), the rc.local file is not enabled by default. If you want to enable auto-start for the management platform in such OS versions, you can perform the following actions:

Create or edit the /etc/rc.local file.

```bash
# sudo vim /etc/rc.local
```

Edit the content in the file, for example:

```shell
#!/bin/bash

exit 0
```

The management platform's auto-start requires executable privileges for rc.local; you can execute the following command:

```bash
# sudo chmod +x /etc/rc.local
```

Enable the rc-local service.

```bash
# sudo systemctl start rc-local
# sudo systemctl enable rc-local
```

During the installation and deployment process of the management platform, it will automatically write the startup program to /etc/rc.local. The absence of this file or lack of executable privileges does not affect deployment.

After the management platform is successfully deployed, you can manually add the following startup command to the self-start configuration file:

```bash
# su {installation user} -c '{installation path}/ycm/monit/bin/monit -c {installation path}/ycm/monit/data/monitrc'
su yashan -c '/opt/ycm/ycm/monit/bin/monit -c /opt/ycm/ycm/monit/data/monitrc'
```

>**Note**：
>
> If the startup auto-start does not take effect, the rc-local service may be affected by the server system configuration or other startup items, and the user needs to address this based on the specific situation.

<span name="timedate" id="timedate" class="yaslink"></span>

### Time Zone Synchronization Settings

It is recommended to set the server's time zone before deploying the management platform.

The correct way to modify the time zone:

1. Use `timedatectl` to check the current system time zone.
2. If Local Time is not CST, execute the command to change the time zone to UTC+8: `timedatectl set-timezone Asia/Shanghai`.

## Software Package Preparation

Please obtain software packages from our technical support based on actual needs: (xx.xx is the software version number)

|Package Name |Description |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| yashandb-cloud-manager-xx.xx-linux-aarch64.tar.gz<br>yashandb-cloud-manager-xx.xx-linux-x86.tar.gz | Management platform installation package for deployment on Linux x86_64/arm64 platforms. |

Upload the package to the yashan user's /home/yashan path:

```shell
# Unzip
$ tar -xzvf yashandb-cloud-manager-xx.xx-linux-x86.tar.gz
$ cd ycm
```

## Client Browser

Supported browsers include Google Chrome, Microsoft Edge, and Firefox; using recent versions is recommended.

|Browser |Supported Version |
| -------------- | ------------- |
| Google Chrome     | Version 88 and above |
| Firefox           | Version 78 and above |
| Microsoft Edge    | Version 88 and above |