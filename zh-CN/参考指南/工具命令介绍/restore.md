restore.sh是平台提供的备份工具，执行脚本可以对管理平台或ycm-agent进行数据和配置的恢复。

## 命令介绍

路径说明：`{管理平台安装路径}/scripts/restore.sh`。

|  参数| 说明|
| ------------------ | ------------------------------------------------------------ |
| -n,--name         | 必传，指定需恢复的组件：YCM或ycm-agent                       |
| -i,--install-path | 必传，组件的安装路径                                         |
| -p,--path         | 必传，备份集的路径，例如/opt/ycm/ycm/bak/ycm-20231221-122304.tar.gz |
| -c,--cluster      | 可选，数据库名称。<br>当后端数据库为YashanDB，执行恢复时才需指定该参数 |
| -y,--yasdb-home   | 可选，后端数据库YashanDB的安装目录。<br>当后端数据库为YashanDB，执行恢复时才需指定该参数 |
| --ip               | 可选，后端YashanDB的IP。<br>当后端数据库为YashanDB，执行恢复时才需指定该参数 |
| --user             | 可选，登录远端YashanDB的机器的SSH用户（需配置SSH免密）。<br>当后端数据库为YashanDB，执行恢复时才需指定该参数 |
| --port             | 可选，登录远端YashanDB的机器的端口，默认是22                 |
| -h,--help         | 查看帮助                                                     |

示例：

```shell
# 备份与恢复ycm-agent
$ /opt/ycm/ycm-agent/scripts/restore.sh -n ycm-agent -i /opt/ycm/ycm-agent -p /opt/ycm/ycm-agent/bak/ycm-agent-20231221-152809.tar.gz
```
