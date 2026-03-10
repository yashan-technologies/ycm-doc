## monit Fails to Start During Initialization of Management Platform (Common in Kylin System)

Error Example: `/opt/ycm/ycm/monit/bin/monit: error while loading shared libraries: libnsl.so.1:cannot open shared object file: No such file or directory`, indicating that the lib library is missing.

Solution:

1. Install the required dependencies:

- Try `yum install libnsl`
- Manually install with `apt update && apt-get -y install rpm  && rpm -ivh libnsl-2.28-164.el8.aarch64.rpm --nodeps`

2. Start monit: `${management platform installation path}/monit/monitctl start`, where the default installation path is `/opt/ycm/ycm`.

**Note**: If there are multiple hosts, ensure that each host has the required lib library.

## Time Zone Jump or Inconsistency with Client

If you find that the time zone is incorrect after deploying the management platform and need to make adjustments, please refer to [Pre-installation Preparation](../Installation and Upgrade/Installation and Uninstallation/Preparation.html#timedate) for time zone adjustments.

**Update Prometheus Data**

Since Prometheus is a time series database, a time jump will cause it to fail to continue collecting data. It needs to be updated and restarted (this operation is required whenever there is a time jump).

```bash
Enter the following commands in the terminal:
$ cd ${management platform installation path}/data
$ rm -rf prometheus
$ cd ../
$ bash ./scripts/prometheus.sh restart
$ bash ./scripts/promtail.sh restart
```

**Update Management Platform Background Database (if SQLite3)**

```bash
Enter the following commands in the terminal:
$ cd ${management platform installation path}/data
$ touch output.sql
$ sqlite3 ycm.db
$ .output output.sql
$ .dump
$ .quit
$ bash ../monit/monitctl stop
$ bash ../scripts/ycm.sh stop
$ mv ycm.db ycm.db.bak
$ touch ycm.db
$ sqlite3 ycm.db
$ .read ./ouput.sql
$ .quit
$ bash ../scripts/ycm.sh start
$ bash ../monit/monitctl start
```
