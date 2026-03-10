backup.sh是平台提供的备份工具，执行脚本可以对管理平台或ycm-agent进行数据和配置的备份。

## 命令介绍

路径说明：`{管理平台安装路径}/scripts/backup.sh`。

|  参数| 说明|
| ----------------- | ------------------------------------------------------------ |
| -n,--name         | 必传，指定需备份的组件：YCM或ycm-agent                       |
| -i,--install-path | 必传，组件的安装路径                                         |
| --dest            | 可选，备份的目标地址，默认为`{管理平台安装路径}/backup`      |
| --cron            | 可选，[cron表达式](../cron表达式)                         |
| -m,--max-age      | 可选，最大保留时间，单位为天，默认为7，备份文件保存期限超出该值后，管理平台会自动清理相应文件。<br/>同一目录下备份文件最大保存份数固定为3，备份成功后若当前目录下存在超过3份备份文件，无论是否超过最大保留时间，系统仍将自动清理最早的备份文件直至仅剩3份备份文件 |
| -c，--cluster     | 可选，数据库名称。<br>当后端数据库为YashanDB，执行备份时才需指定该参数 |
| -y，--yasdb-home  | 可选，后端数据库YashanDB的安装目录<br/>当后端数据库为YashanDB，执行备份时才需指定该参数 |
| --cata-log        | 可选，yasrman所使用的cata log，指定路径生成目录。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| --addr            | 可选，后端数据库YashanDB的yasom的访问地址。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| -k，--key         | 可选，后端数据库YashanDB的yasom的token。可以在YashanDB的安装目录/om/数据库名称/conf/tls.toml中获取。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| -t, --cert        | 可选，后端数据库YashanDB的yasom指定TLS加密通信时，需要指定对应的加密证书。可以在后台YashanDB的安装目录/om/数据库名称/conf/tls.toml中获取。<br>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| -S，--server      | 可选，后端数据库YashanDB的yasom指定TLS加密通信时，需要指定该名称。可以在后台YashanDB的安装目录/om/数据库名称/conf/tls.toml中获取。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| -u，--username    | 可选，数据库用户（拥有备份权限）。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| -p，--password    | 可选，数据库用户密码。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| --ip              | 可选，后端YashanDB的IP。<br/>当后端数据库为YashanDB，初次执行备份时才需指定该参数 |
| --user            | 可选，登录后端YashanDB的机器的SSH用户（需配置SSH免密）。<br/>当后端数据库为YashanDB，执行备份时才需指定该参数 |
| --port            | 可选，登录后端YashanDB的机器的端口，默认是22                 |
| -h，--help        | 查看帮助                                                     |

示例：

```shell
# 首次备份
$ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db  -y /opt/ycm_db/yashandb/23.1.0.311 --cata-log /opt/ycm_db/yashandb/23.1.0.311/catalog --addr 192.168.1.2:1675 -k d30c03533cef25d1 -u sys -p password --user yashan --ip 192.168.1.2

# 后续备份
$ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db  -y /opt/ycm_db/yashandb/23.1.0.311 --user yashan --ip 192.168.1.2
```
