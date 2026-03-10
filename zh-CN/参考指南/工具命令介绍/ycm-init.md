该命令支持在部署时自定义参数，如端口、安装路径等。提供高度自定义的参数，让用户可以通过命令行或Web的方式进行参数修改。

## 限制说明

ycm-init部署需要sudo权限或者root执行。

## 命令介绍

|  命令| 说明|
| --------------------- | --------------------------------------------- |
| ycm-init deploy       | 命令行部署管理平台                            |
| ycm-init web          | 启动Web界面，使用可视化方式进行部署管理平台   |
| ycm-init agent deploy | 命令行部署ycm-agent，手动托管时才需执行该命令 |

###  ycm-init deploy

|  参数| 说明|
| ------------- | ---------------------------------------------------------------------------------------------------------- |
| -h,--help     | 查看帮助                                                                                                   |
| --conf        | 可选，指定初始化服务的配置文件路径，默认为`etc/deploy.yml`                                                 |
| -F,--force    | 可选，是否强制删除安装目录下可能存在的残留文件并继续执行当前操作。不指定该参数时，如遇残留文件将报错并退出 |
| --role        | 可选，主备部署时指定的实例角色，可选值为primary和standby。默认为空，表示非主备部署                         |
| --election-ip | 可选，部署主备模式的实例的选举IP，默认为当前主机可用IP      |
| --purge       | 可选，是否删除后端数据库中已存在的表，默认为false，不删除                                                  |
| -u,--username | 可选，后端数据库用户名，后端数据库为YashanDB时必填                                                         |
| -p,--password | 可选，后端数据库密码，后端数据库为YashanDB时必填                                                           |

示例：

```bash
# 后端数据库为sqlite3
$ sudo ./ycm-init deploy --conf ./etc/deploy.yml

# 后端数据库为YashanDB
$ sudo ./ycm-init deploy --conf ./etc/deploy.yml --username yasman --password password
```

### ycm-init web

|  参数| 说明|
| ---------- | ---------------------------------------------------------------------------------------------------------- |
| -h,--help  | 查看帮助                                                                                                   |
| --addr     | 可选 ，默认值为"0.0.0.0:9001"，启动初始化的Web服务，提供API接口和前端页面                                  |
| -F,--force | 可选，是否强制删除安装目录下可能存在的残留文件并继续执行当前操作。不指定该参数时，如遇残留文件将报错并退出 |

示例：

```bash
$ sudo ./ycm-init web --addr 0.0.0.0:9001
```

> **Note**：
>
> 使用Web部署时，考虑到数据安全性，强烈建议使用HTTPS。

### ycm-init agent deploy

|  参数| 说明|
| ------------------ | ---------------------------------------------------------------------- |
| -h,--help          | 查看帮助                                                               |
| --conf             | 可选，根据配置文件初始化agent服务，默认为`etc/deploy-agent.yml`        |
| -a,--ycp-addr      | 必传，目标平台地址，格式为 "{IP地址}:{端口号}"                         |
| -u,--dba-user      | 可选，当前服务器上数据库的dba权限用户名                                |
| -p,--dba-pwd       | 可选，当前主机数据库的dba权限用户密码                                  |
| -c,--cluster       | 可选，指定数据库名称，当前主机有多个数据库时可指定某一个               |
| --skip-create-user | 可选，跳过创建yasom用户，当指定该参数时可以不传--dba-user以及--dba-pwd |

示例：

```bash
$ sudo ./ycm-init agent deploy --conf etc/deploy-agent.yml -a <YCM_ip>:9060 -u <dba_user> -p <dba_pwd> -c YashanDB
```

> **Note**：
>
> 若部署失败导致安装目录有文件残留，需要手动执行${ycm-agent安装目录}/scripts/clean.sh脚本清理环境。
