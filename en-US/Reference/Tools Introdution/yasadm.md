yasadm is a tool that operates based on the management platform, supporting process start and stop on both the platform and host side, resetting platform user passwords, assisting with offline upgrades of the platform, deleting records from servers and databases, etc.

## Restrictions

- yasadm should be used under the execution directory of the management platform `${YCM_INSTALL_PATH}/scripts/yasadm`, and it is ineffective in the execution directory of the agent. (The upgrade command is special and must be used in the extraction directory of the new package).
- If it is high availability, yasadm must be executed under the main node.

## Command Introduction

Path description: `{YCM_INSTALL_PATH}/scripts/yasadm`.

|Command |Description |
| ---------------- | ----------------------------------- |
| yasadm yasdb       | Operate on the databases that have been added |
| yasadm host        | Operate on the hosts that have been added     |
| yasadm ssh         | Generate SSH configuration file (for offline upgrade operations) |
| yasadm upgrade      | Offline upgrade                              |
| yasadm rollback     | Rollback the failed upgrade of the management platform |
| yasadm ycm         | Start and stop platform-side processes       |
| yasadm ycm-agent   | Start and stop processes on hosted servers   |
| yasadm network      | Network segment management                    |
| yasadm user         | User management                               |
| yasadm auth         | Authentication management                     |
| yasadm instance     | Operate on high availability deployment nodes |
| yasadm password     | Manage passwords in configuration files       |

### yasadm yasdb

<span name="remove" id="remove" class="yaslink"></span>

#### yasadm yasdb remove

Functionality: Remove hosting.

|Parameter |Description |
| -------------- | ------------------------------------------------------------------ |
| -h, --help       | View help                                                                    |
| -n, --name       | Required, the name of the database to remove (refers to the database name in the management platform YashanDB list) |
| -y, --assumeyes  | Optional, skip confirmation prompt, all prompts default to yes              |

#### yasadm yasdb delete

Functionality: If the normal hosting removal process cannot be followed, this command can be used to clear hosting records (only not displayed but has not been removed, used for extreme situations).

|Parameter |Description |
| -------------- | ------------------------------------- |
| -h, --help       | View help                                     |
| -n, --name       | Required, the name of the database to clear   |
| -y, --assumeyes  | Optional, skip confirmation prompt, all prompts default to yes |

### yasadm host

#### yasadm host delete

Functionality: Delete a host managed by the platform.

|Parameter |Description |
| -------------- | ------------------------------------- |
| -h, --help       | View help                                     |
| --hostid         | Required, delete the host's host_id           |
| -y, --assumeyes  | Optional, skip confirmation prompt, all prompts default to yes |

#### yasadm host gen

Functionality: Generate an SSH configuration file containing information about each server (the file can be used with the upgrade, rollback, and ycm-agent commands).

|Parameter |Description |
| ---------------- | -------------------------------------------------- |
| -h, --help        | View help                                          |
| -i, --install-dir | Optional, management platform installation path, default is the current installation package path |
| -u, --username    | Optional, configure the SSH user for the host, default is empty |
| -p, --password    | Optional, configure the SSH password for the host, default is empty |
| --port            | Optional, configure the SSH port for the host, default 22 |
| --rpc-port        | Optional, the RPC port needed to upgrade the ycm-agent host, default 9072 |

Example:

```bash
# Generate toml file template
$ ./yasadm host gen
```

### yasadm upgrade

Functionality: Offline upgrade (requires using yasadm in the new package).

|Parameter |Description |
| --------------------- | ------------------------------------------------------------------------------------------------------- |
| -h, --help                 | View help                                                                                             |
| -i, --install-dir          | Required, platform installation path                                                                  |
| -t, --toml                 | Required, the configuration file of host information, can be generated using the `yasadm host gen` command |
| --skip-task                | Optional, whether to skip checking for running tasks on the platform, default is false, checks for running tasks on the platform (if there are running tasks, the upgrade check fails) |
| -f, --force                | Optional, whether to upgrade directly without confirmation after the check is completed, default is false |
| --imp-exp-install-dir      | The installed YashanDB client path on the management platform server, required when the platform backend database is `yashandb` |
| -p, --plugin-package       | Optional, installation package for systems with different CPU architectures, required if the platform manages both x86_64 and aarch64 hosts |
| --rpc                      | Optional, whether to upgrade via RPC, default is SSH                                               |

Example:

```shell
# Upgrade via SSH
$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yashandb/23.1.1.1
```

### yasadm upgrade clean

Functionality: When performing RPC-based upgrades, clean up failed upgrade processes to avoid port occupation.

|Parameter |Description |
| ---------------- | ----------------------------------------------------------- |
| -h, --help        | View help                                      |
| -i, --install-dir | Optional, platform installation path            |
| -t, --toml        | Required, the configuration file of host information, can be generated using the `yasadm host gen` command |

### yasadm rollback

Functionality: Rollback the failed offline upgrade of the management platform (needs to use yasadm in the new package).

|Parameter |Description |
| --------------------------- | ------------------------------------------------------------------------ |
| -h, --help                      | View help                                                                   |
| -i, --install-dir               | Optional, platform installation path                                        |
| -t, --toml                      | Required, the configuration file of host information, can be generated using the `yasadm host gen` command |
| -f, --force                     | Optional, whether to rollback directly without confirmation after the rollback check is complete, default is false |
| --imp-exp-install-dir           | Optional, path of the database client with imp and exp installed, required when the platform backend database is `yashandb` |
| --skip-upgrade-status-check     | Optional, whether to skip the upgrade status check and force rollback, default is false |

### yasadm ycm

#### yasadm ycm start

Functionality: Start platform-side processes.

|Parameter |Description |
| ------------ | ---------------------------------------------------------------- |
| -h, --help        | View help                                                |
| -p, --process     | Optional, specify the process to start (e.g., 'ycm', 'monit'), if not provided, all processes are started by default |

#### yasadm ycm stop

Functionality: Stop platform-side processes.

|Parameter |Description |
| ------------ | ---------------------------------------------------------------- |
| -h, --help        | View help                                                |
| -p, --process     | Optional, specify the process to stop (e.g., 'ycm', 'monit'), if not provided, all processes are stopped by default |

####  yasadm ycm remote gen

Functionality: Generate configuration file `add_remote_ycm.toml` for associated remote YCM.

|Parameter |Description |
| --------------------- | ---------------------------------------- |
| -h, --help                 | View help                                       |
| -a, --remote-addr          | Required, communication address of the remote YCM, format is `ip:port` |
| -i, --local-ip             | Required, local YCM's communication `ip`       |
| -o, --out                  | The save path for the generated configuration file, default is the current path |
| -lu, --local-user          | Required, local YCM's login user                |
| -lp, --local-password      | Required, local YCM's user password              |
| -ru, --remote-user         | Required, remote YCM's login user                |
| -rp, --remote-password      | Required, remote YCM's user password              |

####  yasadm ycm remote add

Functionality: Associate with a remote YCM.

|Parameter |Description |
| --------------------- | ----------------------- |
| -h, --help                 | View help                        |
| -t, --toml                 | Required, associated configuration file |
| -lu, --local-user          | Required, local YCM's login user |
| -lp, --local-password      | Required, local YCM's user password |
| -ru, --remote-user         | Required, remote YCM's login user |
| -rp, --remote-password      | Required, remote YCM's user password |

####  yasadm ycm remote get

Functionality: View information about the remote YCM.

|Parameter |Description |
| -------------------- | ----------------------- |
| -h, --help                | View help                        |
| -lu, --local-user         | Required, local YCM's login user |
| -lp, --local-password     | Required, local YCM's user password |

####  yasadm ycm remote update

Functionality: Update information about the remote YCM.

|Parameter |Description |
| --------------------- | ----------------------- |
| -h, --help                 | View help                        |
| -t, --toml                 | Required, associated configuration file |
| -lu, --local-user          | Required, local YCM's login user |
| -lp, --local-password      | Required, local YCM's user password |
| -ru, --remote-user         | Required, remote YCM's login user |
| -rp, --remote-password      | Required, remote YCM's user password |

####  yasadm ycm remote remove

Functionality: Remove the associated remote YCM.

|Parameter |Description |
| --------------------- | ------------------------------------ |
| -h, --help                 | View help                                          |
| -lu, --local-user          | Required, local YCM's login user                   |
| -lp, --local-password      | Required, local YCM's user password                |
| -ru, --remote-user         | Required, remote YCM's login user                  |
| -rp, --remote-password      | Required, remote YCM's user password                |
| -f, --force                | Whether to forcibly remove locally, ignoring remote removal errors |

### yasadm ycm-agent

#### yasadm ycm-agent start

Functionality: Start processes on the hosted server.

|Parameter |Description |
| ------------ | ------------------------------------------------------------------------------------ |
| -h, --help        | View help                                                 |
| -a, --addr        | Required, the IP address of the host                     |
| -s, --toml        | Optional, connect to the host based on the ycm_host.toml file, if not provided, default to passwordless connection, can use `yasadm host gen` to generate |
| -p, --process     | Optional, specify the process to start (e.g., 'ycm-agent', 'monit'), if not provided, all processes are started by default |

#### yasadm ycm-agent stop

Functionality: Stop processes on the hosted server.

|Parameter |Description |
| ------------ | ------------------------------------------------------------------------------------ |
| -h, --help        | View help                                                 |
| -a, --addr        | Required, the IP address of the host                     |
| -s, --toml        | Optional, connect to the host based on the ycm_host.toml file, if not provided, default to passwordless connection, can use `yasadm host gen` to generate |
| -p, --process     | Optional, specify the process to stop (e.g., 'ycm-agent', 'monit'), if not provided, all processes are stopped by default |

### yasadm user

#### yasadm user password reset

Functionality: Reset management platform user password.

|Parameter |Description |
| -------------- | ------------------------------------- |
| -h, --help       | View help                                      |
| -n, --name       | Required, specify the user                    |
| -y, --assumeyes  | Optional, skip confirmation prompt, all prompts default to yes |

### yasadm auth

#### yasadm auth twofa close

Functionality: Disable two-factor authentication.

|Parameter |Description |
| -------------- | ------------------------------------- |
| -h, --help       | View help                                      |
| -y, --assumeyes  | Optional, skip confirmation prompt, all prompts default to yes |

### yasadm instance

#### yasadm instance status

Functionality: View basic information of high availability deployment nodes.

|Parameter |Description |
| ------------- | ----------------------------------------------------------- |
| -h, --help        | View help                                             |
| --db-timeout      | The timeout for connecting to the backend database and querying node information, default is 3s |
| --api-timeout     | The timeout for querying node role information through the `api/appconfig` interface, default is 3s |

Example:
  ```shell
$ ./scripts/yasadm instance status
  ```

#### yasadm instance delete

Functionality: Delete metadata of failed deployment instance.

|Parameter |Description |
| -------------- | -------- |
| -h, --help       | View help                                  |
| -i, --inst-id    | Instance id                                 |
| -y, --assumeyes  | Confirm deletion                           |

####  yasadm password reset

Functionality: Modify the encrypted password in the configuration file, requires restarting the YCM process to take effect.

|Parameter |Description |
| ----------------- | -------------------- |
| -h, --help           | View help                                 |
| -n, --new-password   | Required, set the password                |
| -t, --type           | Required, set the password type           |

Among them, the optional parameters supported by the `--type` are:
- backend: Modify the encrypted password for the backend database user in the configuration file `ycm.yaml`, requires restarting the YCM process to take effect.