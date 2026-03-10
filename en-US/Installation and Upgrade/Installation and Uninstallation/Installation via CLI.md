## Single Node Installation and Deployment

### Step One: Modify Configuration File as Needed

```bash
$ vi ./etc/deploy.yml

# Modify configuration
server：
install_path: /opt/ycm      # Default installation path, can be modified to custom path
system_user: yashan          # Change system user to installation user

# If the backend database is yashandb, the following configuration items must also be modified
dbconfig:
    driver: "yashandb"  # The backend must be specified as yashandb when using yashanDB
    url: "192.168.18.177:1688,192.168.18.178:1688,192.168.18.179:1688"   # Includes all primary/standby instances and listening ports of the database
    libPath: "/home/yashan/yashandb-client/lib" # Built-in client does not need configuration, or configure it to the lib path of the client installed by yourself         
```

### Step Two: Deploy Management Platform

**Using SQLite as Backend Database**

```bash
# Use default deployment configuration, using sqlite as backend database
$ sudo ./ycm-init deploy --conf ./etc/deploy.yml
```

**Using YashanDB as Backend Database**

```bash
# Backend database is YashanDB
$ sudo ./ycm-init deploy --conf ./etc/deploy.yml --username yasman --password password
```

Parameter descriptions are as follows:

|Parameter |Description |
| ------------- | ---------------------------------------------------- |
| --conf         | Specify the path to the initialization service configuration file, defaults to `etc/deploy.yml` |
| -u, --username | Backend database username, required when the backend database is YashanDB         |
| -p, --password | Backend database password, required when the backend database is YashanDB          |

**(Optional) Configure HTTPS**

```bash
$ vi ycm/etc/ycm.yaml
# Example of self-signed certificate configuration modification
  httpTLSEnable: true
    httpTLSCertFile: /opt/ycm/ycm//data/cert/grpc.crt
    httpTLSKeyFile: /opt/ycm/ycm//data/cert/grpc.key
```
```bash
# Manually restart the ycm service to take effect
$ /opt/ycm/ycm/monit/monitctl run restart -g ycm
```

### Step Three: Access Management Platform

Once deployed, open a browser and access `HTTP(S):IP:9060` to use the management platform.
![](./image/ycm.png)

## Primary/Standby Mode Installation and Deployment

When deploying primary/standby nodes, the host they are on will be automatically added to the management platform.

### Step One: Deploy Primary Node

**Modify Configuration File as Needed**

Log in to the server at 192.168.1.2 as the installation user, modify the deploy.yml configuration file, configuring the backend database as YashanDB.

```bash
$ vi etc/deploy.toml
# Modify configuration
  dbconfig:
      driver: "yashandb"  # The backend must be specified as yashandb when using yashanDB
      url: "192.168.18.177:1688,192.168.18.178:1688,192.168.18.179:1688"  # Includes all primary/standby instances and listening ports of the database
      libPath: "/home/yashan/yashandb-client/lib" # Built-in client does not need configuration, or configure it to the lib path of the client installed by yourself
```

**Deploy Management Platform**

```bash
$ sudo ./ycm-init deploy --role primary --election-ip 192.168.1.2 --username yasman --password password
```

Parameter descriptions are as follows:

|Parameter |Description |
| ------------- | ----------------------------------------------------------------------------------------------- |
| --role         | The instance role specified during primary/standby deployment, optional values are primary and standby. Default is empty, indicating non-primary/standby deployment |
| --election-ip  | The election IP of the instance deployed in primary/standby mode, defaults to the current host's available IP |
| -u, --username | Backend database username, required when the backend database is YashanDB                             |
| -p, --password | Backend database password, required when the backend database is YashanDB                             |

### Step Two: Deploy Standby Node

**Modify Configuration File as Needed**

Log in to the server at 192.168.1.3 as the installation user, modify the deploy.yml configuration file, configuring the backend database as YashanDB, with the same URL as the primary node.

```bash
$ vi etc/deploy.toml
# Modify configuration
  dbconfig:
      driver: "yashandb"  # The backend must be specified as yashandb when using yashanDB
      url: "192.168.18.177:1688,192.168.18.178:1688,192.168.18.179:1688"  # Includes all primary/standby instances and listening ports of the database   
      libPath: "/home/yashan/yashandb-client/lib" # Built-in client does not need configuration, or configure it to the lib path of the client installed by yourself                        
```

**Deploy Management Platform**

```bash
$ sudo ./ycm-init deploy --role standby --election-ip 192.168.1.3 --username yasman --password password
```
Parameter descriptions are as follows:

|Parameter |Description |
| ------------- | ----------------------------------------------------------------------------------------------- |
| --role         | The instance role specified during primary/standby deployment, optional values are primary and standby. Default is empty, indicating non-primary/standby deployment |
| --election-ip  | The election IP of the instance deployed in primary/standby mode, defaults to the current host's available IP |
| -u, --username | Backend database username, required when the backend database is YashanDB                             |
| -p, --password | Backend database password, required when the backend database is YashanDB                             |

**(Optional) Both primary and standby are configured with HTTPS**

```bash
$ vi ycm/etc/ycm.yaml
# Example of self-signed certificate configuration modification
  httpTLSEnable: true
    httpTLSCertFile: /opt/ycm/ycm//data/cert/grpc.crt
    httpTLSKeyFile: /opt/ycm/ycm//data/cert/grpc.key
```
```bash
# Manually restart the ycm service to take effect
$ /opt/ycm/ycm/monit/monitctl run restart -g ycm
```

### Step Three: Access Management Platform

Once deployed, open a browser and access `HTTP(S)://IP:9060` to use the management platform.

![](./image/ycm.png)
