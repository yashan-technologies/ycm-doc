yasadm是基于管理平台运行的工具，支持平台端和主机端的进程启停，平台用户密码重置，平台的离线升级辅助、服务器和数据库的记录删除等。

## 限制说明

- 应在安装管理平台的执行目录`${管理平台安装路径}/scripts/yasadm`下使用yasadm，在安装agent的执行目录下无效。（升级命令特殊，需在新包的解压目录下使用）。
- 如果是高可用，必须要保证yasadm在主节点下执行。

## 命令介绍

路径说明：`{管理平台安装路径}/scripts/yasadm`。

|  命令| 说明|
| ---------------- | ----------------------------------- |
| yasadm yasdb     | 对已添加的数据库进行相关操作        |
| yasadm host      | 对已添加的主机进行相关操作          |
| yasadm ssh       | 生成SSH配置文件（用于离线升级操作） |
| yasadm upgrade   | 离线升级                            |
| yasadm rollback  | 回退升级失败的管理平台              |
| yasadm ycm       | 启停平台端的进程                    |
| yasadm ycm-agent | 启停被托管服务器的进程              |
| yasadm network   | 网段管理                            |
| yasadm user      | 用户管理                            |
| yasadm auth      | 认证管理                            |
| yasadm instance  | 对高可用部署节点进行的相关操作      |
| yasadm password  | 对配置文件中的密码进行管理          |

### yasadm yasdb

<span name="remove" id="remove" class="yaslink"></span>

#### yasadm yasdb remove

功能：移除托管。

|  参数| 说明|
| -------------- | ------------------------------------------------------------------ |
| -h,--help      | 查看帮助                                                           |
| -n,--name      | 必传，移除托管的数据库名称（指管理平台YashanDB列表中的数据库名称） |
| -y,--assumeyes | 可选，跳过确认询问，所有询问默认为yes                              |

#### yasadm yasdb delete

功能：若无法走正常的移除托管流程，可使用该命令清除托管记录（仅不显示但并未移除托管，用来应对极端情况）。

|  参数| 说明|
| -------------- | ------------------------------------- |
| -h,--help      | 查看帮助                              |
| -n,--name      | 必传，清除信息的数据库名称            |
| -y,--assumeyes | 可选，跳过确认询问，所有询问默认为yes |

### yasadm host

#### yasadm host delete

功能：删除平台管理的主机。

|  参数| 说明|
| -------------- | ------------------------------------- |
| -h,--help      | 查看帮助                              |
| --hostid       | 必传，删除主机的host_id               |
| -y,--assumeyes | 可选，跳过确认询问，所有询问默认为yes |

#### yasadm host gen

功能：生成含各服务器信息的SSH配置文件（文件可配合upgrade，rollback，ycm-agent命令使用）。

|  参数| 说明|
| ---------------- | -------------------------------------------------- |
| -h,--help        | 查看帮助                                           |
| -i,--install-dir | 可选，管理平台安装路径，默认是当前安装包所在路径   |
| -u,--username    | 可选，配置主机的SSH用户，默认为空                  |
| -p,--password    | 可选，配置主机的SSH密码，默认为空                  |
| --port           | 可选，配置主机的SSH端口，默认22                    |
| --rpc-port       | 可选，升级ycm-agent主机所需要的rpc端口，默认为9072 |

示例：

```bash
# 生成toml文件模版
$ ./yasadm host gen
```

### yasadm upgrade

功能：离线升级（需要使用升级新包中的yasadm执行）。

|  参数| 说明|
| --------------------- | ------------------------------------------------------------------------------------------------------- |
| -h,--help             | 查看帮助                                                                                                |
| -i,--install-dir      | 必传，平台安装路径                                                                                      |
| -t,--toml             | 必传，主机信息的配置文件，可以通过`yasadm host gen`命令生成                                             |
| --skip-task           | 可选，是否跳过检查平台存在运行的任务，默认为false，检查平台运行任务（存在运行的任务，升级前检查不通过） |
| -f,--force            | 可选，升级检查完成后，是否不经过确认，直接升级，默认为false                                             |
| --imp-exp-install-dir | 管理平台服务器上安装的YashanDB客户端路径，平台后端数据库为`yashandb`时必填                              |
| -p,--plugin-package   | 可选，其它CPU架构的系统安装包，如果平台同时管理了x86_64和aarch64主机，该参数必填                        |
| --rpc                 | 可选，是否通过rpc方式升级，默认为SSH方式                                                                |

示例：

```shell
# 通过ssh方式升级
$ ./scripts/yasadm upgrade -i /opt/ycm -t ycm_host.toml --imp-exp-install-dir /home/yashan/yashandb/23.1.1.1
```

### yasadm upgrade clean

功能：在执行rpc方式升级时，清理部署失败的升级进程，避免升级端口被占用。

|  参数| 说明|
| ---------------- | ----------------------------------------------------------- |
| -h,--help        | 查看帮助                                                    |
| -i,--install-dir | 可选，平台安装路径                                          |
| -t,--toml        | 必传，主机信息的配置文件，可以通过`yasadm host gen`命令生成 |

### yasadm rollback

功能：回退离线升级失败的管理平台（需要使用升级新包中的yasadm执行）。

|  参数| 说明|
| --------------------------- | ------------------------------------------------------------------------ |
| -h,--help                   | 查看帮助                                                                 |
| -i,--install-dir            | 可选，平台安装路径                                                       |
| -t,--toml                   | 必传，主机信息的配置文件，可以通过`yasadm host gen`命令生成              |
| -f,--force                  | 可选，回退检查完成后，是否不经过确认，直接回退，默认为false              |
| --imp-exp-install-dir       | 可选，安装有imp和exp的数据库客户端路径，平台后端数据库为`yashandb`时必填 |
| --skip-upgrade-status-check | 可选，是否跳过升级状态检查，进行强制回退，默认为false                    |

### yasadm ycm

#### yasadm ycm start

功能：启动平台端的进程。

|  参数| 说明|
| ------------ | ---------------------------------------------------------------- |
| -h,--help    | 查看帮助                                                         |
| -p,--process | 可选，指定启动的进程（如'ycm'，'monit'），不传则默认启动所有进程 |

#### yasadm ycm stop

功能：停止平台端的进程。

|  参数| 说明|
| ------------ | ---------------------------------------------------------------- |
| -h,--help    | 查看帮助                                                         |
| -p,--process | 可选，指定启动的进程（如'ycm'，'monit'），不传则默认停止所有进程 |

####  yasadm ycm remote gen

功能：生成关联异地YCM的配置文件`add_remote_ycm.toml`。

|  参数| 说明|
| --------------------- | ---------------------------------------- |
| -h,--help             | 查看帮助                                 |
| -a,--remote-addr      | 必传，异地YCM的通信地址，格式为`ip:port` |
| -i,--local-ip         | 必传，本地YCM的通信`ip`                  |
| -o,--out              | 生成的配置文件保存路径，默认为当前路径   |
| -lu,--local-user      | 必传，本地YCM的登录用户                  |
| -lp,--local-password  | 必传，本地YCM的用户密码                  |
| -ru,--remote-user     | 必传，异地YCM的登录用户                  |
| -rp,--remote-password | 必传，异地YCM的用户密码                  |

####  yasadm ycm remote add

功能：关联异地YCM。

|  参数| 说明|
| --------------------- | ----------------------- |
| -h,--help             | 查看帮助                |
| -t,--toml             | 必传，关联配置文件      |
| -lu,--local-user      | 必传，本地YCM的登录用户 |
| -lp,--local-password  | 必传，本地YCM的用户密码 |
| -ru,--remote-user     | 必传，异地YCM的登录用户 |
| -rp,--remote-password | 必传，异地YCM的用户密码 |

####  yasadm ycm remote get

功能：查看异地YCM的信息。

|  参数| 说明|
| -------------------- | ----------------------- |
| -h,--help            | 查看帮助                |
| -lu,--local-user     | 必传，本地YCM的登录用户 |
| -lp,--local-password | 必传，本地YCM的用户密码 |

####  yasadm ycm remote update

功能：更新异地YCM的信息。

|  参数| 说明|
| --------------------- | ----------------------- |
| -h,--help             | 查看帮助                |
| -t,--toml             | 必传，关联配置文件      |
| -lu,--local-user      | 必传，本地YCM的登录用户 |
| -lp,--local-password  | 必传，本地YCM的用户密码 |
| -ru,--remote-user     | 必传，异地YCM的登录用户 |
| -rp,--remote-password | 必传，异地YCM的用户密码 |

####  yasadm ycm remote remove

功能：移除关联的异地YCM。

|  参数| 说明|
| --------------------- | ------------------------------------ |
| -h,--help             | 查看帮助                             |
| -lu,--local-user      | 必传，本地YCM的登录用户              |
| -lp,--local-password  | 必传，本地YCM的用户密码              |
| -ru,--remote-user     | 必传，异地YCM的登录用户              |
| -rp,--remote-password | 必传，异地YCM的用户密码              |
| -f,--force            | 是否强制本地移除，忽略异地移除的报错 |

### yasadm ycm-agent

#### yasadm ycm-agent start

功能：启动被托管服务器的进程。

|  参数| 说明|
| ------------ | ------------------------------------------------------------------------------------ |
| -h,--help    | 查看帮助                                                                             |
| -a,--addr    | 必传，主机的IP地址                                                                   |
| -s,--toml    | 可选，根据ycm_host.toml文件连接主机，不传则默认免密连接，可使用`yasadm host gen`生成 |
| -p,--process | 可选，指定启动的进程（如'ycm-agent'，'monit'），不传则默认启动所有进程               |

#### yasadm ycm-agent stop

功能：停止被托管服务器的进程。

|  参数| 说明|
| ------------ | ------------------------------------------------------------------------------------ |
| -h,--help    | 查看帮助                                                                             |
| -a,--addr    | 必传，主机的IP地址                                                                   |
| -s,--toml    | 可选，根据ycm_host.toml文件连接主机，不传则默认免密连接，可使用`yasadm host gen`生成 |
| -p,--process | 可选，指定停止的进程（如'ycm-agent'，'monit'），不传则默认停止所有进程               |

### yasadm user

#### yasadm user password reset

功能：重置管理平台用户密码。

|  参数| 说明|
| -------------- | ------------------------------------- |
| -h,--help      | 查看帮助                              |
| -n,--name      | 必传，指定用户                        |
| -y,--assumeyes | 可选，跳过确认询问，所有询问默认为yes |

### yasadm auth

#### yasadm auth twofa close

功能：关闭双因子认证。

|  参数| 说明|
| -------------- | ------------------------------------- |
| -h,--help      | 查看帮助                              |
| -y,--assumeyes | 可选，跳过确认询问，所有询问默认为yes |

### yasadm instance

#### yasadm instance status

功能：查看高可用部署节点的基本信息。

|  参数| 说明|
| ------------- | ----------------------------------------------------------- |
| -h,--help     | 查看帮助                                                    |
| --db-timeout  | 连接后端数据库查询节点信息的超时时间，默认为3s              |
| --api-timeout | 通过接口`api/appconfig`查询节点角色信息的超时时间，默认为3s |

示例:
  ```shell
$ ./scripts/yasadm instance status
  ```

#### yasadm instance delete

功能：删除部署失败的实例元数据。

|  参数| 说明|
| -------------- | -------- |
| -h,--help      | 查看帮助 |
| -i,--inst-id   | 示例id   |
| -y,--assumeyes | 确认删除 |

####  yasadm password reset

功能：修改配置文件中的加密密码，需要重启YCM进程生效。

|  参数| 说明|
| ----------------- | -------------------- |
| -h,--help         | 查看帮助             |
| -n,--new-password | 必传，设置的密码     |
| -t,--type         | 必传，设置的密码类型 |

其中，参数`--type`支持的可选参数有：
- backend: 修改配置文件`ycm.yaml`中后端数据库用户的加密密码，需要重启YCM进程生效。
