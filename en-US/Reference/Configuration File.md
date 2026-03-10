## deploy.yml

**Path**: {Installation package path}/ycm/etc/deploy.yml

**server**

|Field |Description |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ycm_name                                     | Product name                                                                                                                                                                                                                                                                                                                                              |
| ycm_name_en                                  | Product English name                                                                                                                                                                                                                                                                                                                                      |
| product_short_name                           | Abbreviated product name, not recommended for modification                                                                                                                                                                                                                                                                                               |
| product_short_name_en                        | Abbreviated product name in English, not recommended for modification                                                                                                                                                                                                                                                                                     |
| install_path                                 | Server installation path                                                                                                                                                                                                                                                                                                                                   |
| system_user                                  | User running server-related services                                                                                                                                                                                                                                                                                                                      |
| ycm_port                                     | Server listening port, default is 9060                                                                                                                                                                                                                                                                                                                  |
| prometheus_port                              | Monitoring listener port, default is 9061                                                                                                                                                                                                                                                                                                              |
| loki_http_port                               | Log analysis HTTP service port, default is 9062                                                                                                                                                                                                                                                                                                         |
| loki_grpc_port                               | Log analysis gRPC service port, default is 9063                                                                                                                                                                                                                                                                                                         |
| yasdb_exporter_port                          | Database information collection port, default is 9064                                                                                                                                                                                                                                                                                                   |
| yas_hcs_api_port                             | yas-hcs-api port, default is 9065                                                                                                                                                                                                                                                                                                                       |
| prometheus_collect_interval                  | Monitoring collection frequency, range of values [1,43200], default is 10                                                                                                                                                                                                                                                                                 |
| prometheus_data_expired                      | Monitoring data retention days, range of values [1,360], default is 180 days                                                                                                                                                                                                                                                                             |
| monit_enable                                 | Whether the server enables monit, enabled by default                                                                                                                                                                                                                                                                                                     |
| grpc_tls_enable                              | Server uses TLS for communication with the host                                                                                                                                                                                                                                                                                                          |
| http_tls_enable                              | Server listens using HTTPS                                                                                                                                                                                                                                                                                                                                |
| manage_ip                                    | Elastic IP, this value needs to be configured when VPC networks are not reachable                                                                                                                                                                                                                                                                         |
| dbconfig: <br>- dirver<br>- url<br>- libpath | Backend database information:<br>- Backend database type, can be set as sqlite3 or yashandb, defaults to sqlite3<br>- When driver is yashandb, this parameter is required. Single IP: ip:port, HA multiple IP: ip:port,ip:port,ip:port or primary:ip:port,ip:port,ip:port. Supports using remote YashanDB as backend, IP address can be non-local<br>- When driver is yashandb, defaults to {Management platform installation path}/ycm/tool/yashandb-client/lib |

**client**

The following parameters are the default parameters applied when adding a new host, which can be modified during the addition.

|Field |Description |
| -------------------- | -------------------------------------------------- |
| install_path        | Default installation path for YCM agent      |
| system_user         | Default running user for YCM agent, can be modified when adding a new host    |
| system_group: yashan| Default running user group for YCM agent, can be modified when adding a new host |
| agent_port          | Default port for YCM agent, default is 9070  |
| export_port         | Default port for YCM collector, default is 9071 |

**user**

|Field |Description |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| superadmin: <br>- password<br>- nickname<br>- email<br>- phone | Configure super administrator information:<br>- Username and password are admin, display as ciphertext, it is recommended to change the password after deployment succeeds<br>- Defaults to superman<br>- Email<br>- Phone |
| token_key                                                  | Encryption string for generating login token, used for obfuscation                                                             |
| token_expired                                              | Token inactive expiration time, range of values [1,1440], default is 30 minutes                                              |
| password_expired                                           | Password validity period, must change after expiration, range of values [1,365], default is 60 days                          |
| retry_times                                                | Number of retries allowed after incorrect password input, range of values [5,30], default is 5 times                          |
| lock_expired                                               | Wait time after being locked for incorrect input, range of values [3,10080], default is 3 minutes                           |

**log**

|Field |Description |
| ------------ | -------------------------------------------- |
| console       | Whether to print logs                          |
| level         | Log level: DEBUG, INFO, WARN, ERROR            |
| rotationTime  | Log retention time, less than or equal to 0 means no expiration, default is 7 days |
| rotationSize  | Maximum size of each log file, minimum value is 10M  |
| maxBackupNum  | Maximum number of log backups, less than or equal to 0 means unlimited  |

