This command supports customizing parameters during deployment, such as port, installation path, etc. It provides highly customizable parameters that allow users to modify parameters via the command line or web interface.

## Restrictions

The ycm-init deployment requires sudo privilege or must be executed as root.

## Command Introduction

|Command |Description |
| --------------------- | --------------------------------------------- |
| ycm-init deploy         | Command line deployment management platform              |
| ycm-init web            | Start web interface for visual deployment management platform |
| ycm-init agent deploy   | Command line to deploy ycm-agent, only necessary when manually hosting |

###  ycm-init deploy

|Parameter |Description |
| ------------- | ---------------------------------------------------------------------------------------------------------- |
| -h,--help      | View help                                                                                                   |
| --conf         | Optional, specify the configuration file path for initializing the service, defaults to `etc/deploy.yml`   |
| -F,--force     | Optional, whether to forcibly delete any residual files in the installation directory and continue the current operation. If this parameter is not specified and residual files are encountered, an error will be reported and the operation will exit. |
| --role         | Optional, the instance role specified during primary/standby deployment, acceptable values are primary and standby. Defaults to empty, indicating non-primary/standby deployment. |
| --election-ip  | Optional, the election IP for instances in primary/standby mode, defaults to the current host's available IP . |
| --purge        | Optional, whether to delete existing tables in the backend database, defaults to false, meaning do not delete. |
| -u,--username  | Optional, backend database username, required when the backend database is YashanDB.                         |
| -p,--password  | Optional, backend database password, required when the backend database is YashanDB.                         |

Example:

```bash
# Backend database is sqlite3
$ sudo ./ycm-init deploy --conf ./etc/deploy.yml

# Backend database is YashanDB
$ sudo ./ycm-init deploy --conf ./etc/deploy.yml --username yasman --password password
```

### ycm-init web

|Parameter |Description |
| ---------- | ---------------------------------------------------------------------------------------------------------- |
| -h,--help      | View help                                                                                                   |
| --addr         | Optional, defaults to "0.0.0.0:9001", starts the initialized web service, providing API interface and frontend page. |
| -F,--force     | Optional, whether to forcibly delete any residual files in the installation directory and continue the current operation. If this parameter is not specified and residual files are encountered, an error will be reported and the operation will exit. |

Example:

```bash
$ sudo ./ycm-init web --addr 0.0.0.0:9001
```

> **Note**：
>
> When using web deployment, it is strongly recommended to use https for data security.

### ycm-init agent deploy

|Parameter |Description |
| ------------------ | ---------------------------------------------------------------------- |
| -h,--help               | View help                                                        |
| --conf                  | Optional, initializes the agent service according to the configuration file, defaults to `etc/deploy-agent.yml` |
| -a,--ycp-addr          | Required, target platform address, format "{IP address}:{port number}" |
| -u,--dba-user          | Optional, dba privilege username of the database on the current server. |
| -p,--dba-pwd           | Optional, dba privilege password of the database on the current host. |
| -c,--cluster           | Optional, specify the database name, can be specified when there are multiple databases on the current host. |
| --skip-create-user     | Optional, skip creating the yasom user. When this parameter is specified, --dba-user and --dba-pwd do not need to be passed. |

Example:

```bash
$ sudo ./ycm-init agent deploy --conf etc/deploy-agent.yml -a <YCM_ip>:9060 -u <dba_user> -p <dba_pwd> -c YashanDB
```

> **Note**：
>
> If the deployment fails and results in residual files in the installation directory, you need to manually execute the ${ycm-agent installation directory}/scripts/clean.sh script to clean the environment.