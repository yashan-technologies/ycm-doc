Unless otherwise specified, the following steps should be performed in the environment where the yasadm tool is located. The paths, users, passwords, ports, etc., in the commands should be replaced with actual values.

This article uses the example of upgrading from 23.2.6.100 to 23.2.7.100.

## SSH Upgrade Method (Recommended)

### Step 1: Upload the New Version Installation Package

1. On the deployment management platform server, create an empty directory that will serve as a temporary storage path for the upgrade installation package. Note that this directory must not be located under the installation directory of the management platform.

  ```shell
$ mkdir ~/upgrade
  ```

2. Upload the new version installation package to this directory.

3. Execute the following command to extract the installation package.

  ```shell
$ cd ~/upgrade
$ tar -zxf yashandb-cloud-manager-23.2.7.100-linux-x86_64.tar.gz
  ```

### Step 2: Generate Configuration File

This step must be performed in the temporary storage directory of the upgrade installation package.

1. Execute the following command to enter the temporary storage directory of the upgrade installation package:

```bash
$ cd ~/upgrade/ycm
```

2. Execute the following command to generate the configuration file for the host information.

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

3. Modify the configuration file information according to actual needs.

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

### Step 3: Execute Offline Upgrade

> **Caution**：
>
> If the upgrade fails, it cannot be upgraded again; only [rollback](#back) is possible.

This step must be performed in the temporary storage directory of the upgrade installation package.

1. Execute the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the upgrade command.

```bash
# Upgrade via SSH

# Backend database is SQLite
$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml

# Backend database is YashanDB
$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yasdb_home
```

Brief parameter introduction:

```bash
-i, --install-dir     Installation path of the management platform
-t, --toml            Configuration file for the managed server information
--imp-exp-install-dir Path to the YashanDB client with imp and exp installed, used for backing up platform backend YashanDB data
```

### Step 4: Check the Version After Upgrade

Open a browser, access and log in to the management platform, click on the user avatar in the top right corner, click **[ About ]**, and check the version after the upgrade.

## RPC Upgrade Method

### Step 1: Upload the New Version Installation Package

1. On the deployment management platform server, create an empty directory that will serve as a temporary storage path for the upgrade installation package.

  ```shell
$ mkdir ~/upgrade
  ```

2. Upload the new version installation package to this directory.

3. Execute the following command to extract the installation package.

  ```shell
$ cd ~/upgrade
$ tar -zxf yashandb-cloud-manager-23.2.7.100-linux-x86_64.tar.gz
  ```

### Step 2: Generate Configuration File

This step must be performed in the temporary storage directory of the upgrade installation package.

1. Execute the following command to enter the temporary storage directory of the upgrade installation package:

```bash
$ cd ~/upgrade/ycm
```

2. Execute the following command to generate the configuration file for the managed server.

  ```bash
# Generate ycm_host.toml in the current directory
$ ./scripts/yasadm host gen -i /opt/ycm --rpc-port 9072
  ```

   Brief parameter introduction:

  ```bash
-i, --install-dir Installation path of the management platform
--rpc-port        Management platform server's upgrade RPC port, default is 9072
  ```

3. Modify the configuration file information according to actual needs.

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

### Step 3: Execute Offline Upgrade

> **Caution**：
>
> If the upgrade fails, it cannot be upgraded again; only [rollback](#back) is possible.

This step must be performed in the temporary storage directory of the upgrade installation package.

1. Execute the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the upgrade command.

```bash
# Upgrade via RPC

# Backend database is SQLite
$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --rpc

# Backend database is YashanDB
$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yasdb_home --rpc
```

Brief parameter introduction:

```bash
-i, --install-dir     Installation path of the management platform
-t, --toml            Configuration file for the managed server information
--rpc                 Upgrade via RPC, defaults to SSH mode
--imp-exp-install-dir Path to the YashanDB client with imp and exp installed, used for backing up platform backend YashanDB data
```

### Step 4: Check the Version After Upgrade

Open a browser, access and log in to the management platform, click on the user avatar in the top right corner, click **[ About ]**, and check the version after the upgrade.

<span id="back" name="back" class="yaslink"></span>

## Upgrade Rollback

> **Note**：
>
> After a successful upgrade, rollback is not supported; rollback is only allowed if the upgrade fails.

During the upgrade process of the management platform, upgrade failures may occur due to command errors, configuration file errors, or version errors. In this case, please run the rollback command to restore the environment to the pre-upgrade state.

1. Execute the following command to enter the temporary storage directory of the upgrade installation package:

```shell
$ cd ~/upgrade/ycm
```

2. Execute the rollback command.

```bash
# Backend database is SQLite
$ ./scripts/yasadm rollback -i /opt/ycm -t ycm_host.toml

# Backend database is YashanDB
$ ./scripts/yasadm rollback -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yasdb_home
```

Brief parameter introduction:

```bash
-i, --install-dir      Installation path of the management platform
-t, --toml             SSH configuration file for the managed server information
--imp-exp-install-dir  Path to the YashanDB client with imp and exp installed, used to restore platform backend YashanDB data
```

> **Note**：
>
> - Rollback after the upgrade failure is allowed.
> - If multiple rollbacks fail, please contact management platform technical support for resolution.