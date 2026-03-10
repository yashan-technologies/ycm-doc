`restore.sh` is a backup tool provided by the platform. Executing the script can restore data and configurations for the management platform or ycm-agent.

## Command Description

Path description: `{Management Platform Installation Path}/scripts/restore.sh`.

|Parameter |Description |
| ------------------ | ------------------------------------------------------------ |
| -n, --name         | Required. Specify the component to be restored: ycm or ycm-agent |
| -i, --install-path | Required. The installation path of the component.         |
| -p, --path         | Required. The path of the backup set, e.g., /opt/ycm/ycm/bak/ycm-20231221-122304.tar.gz |
| -c, --cluster      | Optional. Database name.<br> This parameter must be specified when the backend database is YashanDB during the restore. |
| -y, --yasdb-home   | Optional. The installation directory of the backend database YashanDB.<br> This parameter must be specified when the backend database is YashanDB during the restore. |
| --ip                | Optional. IP of the backend YashanDB.<br> This parameter must be specified when the backend database is YashanDB during the restore. |
| --user              | Optional. SSH user for logging into the remote YashanDB machine (SSH passwordless login must be configured).<br> This parameter must be specified when the backend database is YashanDB during the restore. |
| --port              | Optional. The port for logging into the remote YashanDB machine, default is 22. |
| -h, --help         | View help.                                                 |

Example:

```shell
# Backup and restore ycm-agent
$ /opt/ycm/ycm-agent/scripts/restore.sh -n ycm-agent -i /opt/ycm/ycm-agent -p /opt/ycm/ycm-agent/bak/ycm-agent-20231221-152809.tar.gz
```
