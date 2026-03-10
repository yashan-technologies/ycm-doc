backup.sh is a backup tool provided by the platform. Running the script can back up the data and configuration of the management platform or ycm-agent.

## Command Introduction

Path description: `{Management Platform Installation Path}/scripts/backup.sh`.

|Parameter |Description |
| ----------------- | ------------------------------------------------------------ |
| -n, --name        | Required, specify the component to be backed up: ycm or ycm-agent |
| -i, --install-path| Required, the installation path of the component        |
| --dest            | Optional, the target address for backup, default is `{Management Platform Installation Path}/backup` |
| --cron            | Optional, [cron expression](../Cron Expression)        |
| -m, --max-age     | Optional, max retention time in days, default is 7. The management platform will automatically clean up the corresponding files after the backup file retention period exceeds this value. <br/> The maximum number of backup files kept in the same directory is fixed at 3. After a successful backup, if there are more than 3 backup files in the current directory, regardless of whether the maximum retention time has been exceeded, the system will automatically clean the earliest backup files until only 3 backup files remain. |
| -c, --cluster     | Optional, the database name.<br> This parameter needs to be specified only when the backend database is YashanDB. |
| -y, --yasdb-home  | Optional, the installation directory of the backend database YashanDB.<br/> This parameter needs to be specified only when the backend database is YashanDB. |
| --cata-log        | Optional, the cata log used by yasrman, specifies the path to generate the directory.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| --addr            | Optional, the access address of yasom for the backend database YashanDB.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| -k, --key         | Optional, the token for yasom of the backend database YashanDB. It can be obtained from YashanDB's installation directory/om/database_name/conf/tls.toml.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| -t, --cert        | Optional, the encrypted certificate needed when yasom for the backend database YashanDB specifies TLS encrypted communication. It can be obtained from the installation directory/om/database_name/conf/tls.toml of YashanDB in the background.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| -S, --server      | Optional, the name required for TLS encrypted communication for yasom of the backend database YashanDB. It can be obtained from YashanDB's installation directory/om/database_name/conf/tls.toml in the background.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| -u, --username    | Optional, database user (with backup privilege).<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| -p, --password    | Optional, database user password.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| --ip              | Optional, the IP of the backend YashanDB.<br/> This parameter needs to be specified only when the backend database is YashanDB during the first backup execution. |
| --user            | Optional, SSH user for logging into the machine of the backend YashanDB (SSH passwordless must be configured).<br/> This parameter needs to be specified only when the backend database is YashanDB during the backup execution. |
| --port            | Optional, port for logging into the machine of the backend YashanDB, default is 22 |
| -h, --help        | View help                                               |

Example:

```shell
# First backup
$ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db  -y /opt/ycm_db/yashandb/23.1.0.311 --cata-log /opt/ycm_db/yashandb/23.1.0.311/catalog --addr 192.168.1.2:1675 -k d30c03533cef25d1 -u sys -p password --user yashan --ip 192.168.1.2

# Subsequent backups
$ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db  -y /opt/ycm_db/yashandb/23.1.0.311 --user yashan --ip 192.168.1.2
```
