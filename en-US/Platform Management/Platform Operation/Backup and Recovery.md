The data and configuration related to the management platform (ycm-agent) on the management platform and hosted resources can be backed up and restored using the backup.sh and restore.sh tools. For specific command descriptions, please refer to [backup](../../Reference/Tools Introdution/backup) and [restore](../../Reference/Tools Introdution/restore).

Backup and restore operations do not guarantee strong consistency of data, so please plan these operations wisely. For example, backing up the management platform after adding a hosted resource, and then removing that resource before restoring the management platform, will not restore that resource and may also cause dirty data.

>**Note**:
>
> The installation paths, backup file storage locations, backup file names, backend database related information, etc., in this document are all examples; please refer to the actual setup.

## Backup and Restore Management Platform

### Backup

#### Precautions

- The target address for backup must have more than 2G of free space. If not specified, the default will be `{Management Platform Installation Path (this document uses /opt/ycm as an example)}/backup`.

- To maximize data consistency, do not perform backups during [tasks](../Platform Operation/Scheduling Management/Task Management).

- The steps for backup depend on the backend database configuration of the management platform. Before the operation, obtain specific backend database information (this can be done by checking the dbconfig related configuration in the etc/deploy.toml file).

- When the backend database is remote YashanDB, you need to configure passwordless login before performing the backup operation. The server that allows the management platform to perform restore operations must be able to SSH without a password to the remote YashanDB server. Specific operations depend on the operating system; this document will not elaborate further.

#### Operation Steps

**Scenario 1: Backend Database is SQLite**

1. Log in to the server where the management platform is located as the installation user.

2. Execute the following command to back up the management platform.

    ```shell
    $ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm
    ```

**Scenario 2: Backend Database is Local YashanDB**

1. Log in to the server where the management platform is located as the installation user.

2. Execute the following command to back up the management platform.

    - First backup
        ```shell
        # Configure environment variables
        $ source /data/ycm_db/yashandb/23.2.1.100/conf/YCM_YASDB.bashrc
        
        $ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db -y /data/ycm_db/yashandb/23.2.1.100 --cata-log /data/ycm_db/yashandb/23.2.1.100/catalog --addr 192.168.1.2:1675 -k d30c03533cef25d1 -u sys -p password
        ```
        
    - n+1 backups
        ```shell
        $ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db -y /data/ycm_db/yashandb/23.2.1.100
        ```

**Scenario 3: Backend Database is Remote YashanDB**

1. Log in to the server where the management platform is located as the installation user.

2. Configure passwordless login: Allow the server that executes the backup script to SSH without a password to the remote YashanDB server.

3. Execute the following command to back up the management platform.

    - First backup
        ```shell
        $ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db  -y /data/ycm_db/yashandb/23.2.1.100 --cata-log /data/ycm_db/yashandb/23.2.1.100/catalog --addr 192.168.18.177:1675 -k d30c03533cef25d1 -u sys -p password --user yashan --ip 192.168.18.177
        ```

    - n+1 backups
        ```shell
        $ /opt/ycm/ycm/scripts/backup.sh -n ycm -i /opt/ycm/ycm -c ycm_db  -y /data/ycm_db/yashandb/23.2.1.100 --user yashan --ip 192.168.18.177
        ```

### Restore

#### Precautions

- Before restoring, you need to obtain the storage path of the backup file.

- The steps for restore depend on the backend database configuration of the management platform. Before the operation, confirm the specific backend database information (this can be done by checking the dbconfig related configuration in the etc/deploy.toml file).

- When the backend database is remote YashanDB, you need to configure passwordless login before performing the restore operation. The server that allows the management platform to perform restore operations must be able to SSH without a password to the remote YashanDB server. Specific operations depend on the operating system; this document will not elaborate further.

- After the restore is completed, the related service processes will automatically restart, and **the management platform will be unavailable during this time**, so please arrange the operation time reasonably.

- To maximize data consistency, do not perform restores during [tasks](../Platform Operation/Scheduling Management/Task Management).

#### Operation Steps

**Scenario 1: Backend Database is SQLite**

1. Log in to the server where the management platform is located as the installation user.

2. Execute the following command to restore the management platform.

    ```shell
    $ /opt/ycm/ycm/scripts/restore.sh -n ycm -i /opt/ycm/ycm -p /opt/ycm/ycm/backup/ycm-20231221-144408.tar.gz
    ```

**Scenario 2: Backend Database is Local YashanDB**

1. Log in to the server where the management platform and the backend YashanDB database are located as the installation user.

2. Enter the database installation directory and execute the backup query command to confirm whether the YashanDB backup file exists.
   ```shell
   # The specific value of tag can be obtained from the backup file name
   $ ./bin/yasrman sys/password@192.168.1.2:1688 -c "LIST BACKUP TAG '20231221-151101'"
   ```

3. Execute the following command to prepare the environment.
   ```shell
   $ ./bin/yasboot cluster clean -c ycm_db --restore
   ```

4. Execute the following command to restore the management platform.

   ```shell
   $ /opt/ycm/ycm/scripts/restore.sh -n ycm -i /opt/ycm/ycm -p /opt/ycm/ycm/backup/ycm-20231221-151101.tar.gz -y /data/ycm_db/yashandb/23.2.1.100 -c ycm_db
   ```

**Scenario 3: Backend Database is Remote YashanDB**

1. Log in to the server where the backend YashanDB database is located.

2. Enter the database installation directory and execute the backup query command to confirm whether the YashanDB backup file exists.
   ```shell
   # The specific value of tag can be obtained from the backup file name
   $ ./bin/yasrman sys/password@192.168.18.177:1688 -c "LIST BACKUP TAG '20231221-151101'"
   ```

3. Execute the following command to clean the corresponding database data and start the database in nomount mode to provide a restore environment.
   ```shell
   $ ./bin/yasboot cluster clean -c ycm_db --restore
   ```

4. Log in to the server where the management platform is located as the installation user.

5. Execute the following command to restore the management platform.

   ```shell
   $ /opt/ycm/ycm/scripts/restore.sh -n ycm -i /opt/ycm/ycm -p /opt/ycm/ycm/backup/ycm-20231221-151101.tar.gz -y /data/ycm_db/yashandb/23.2.1.100 -c ycm_db --user yashan --ip 192.168.18.177
   ```

## Backup and Restore ycm-agent

### Backup

#### Precautions

The target address for backup must have more than 1G of free space. If not specified, the default will be `{ycm-agent Installation Path (this document uses /opt/ycm as an example)}/backup`.

#### Operation Steps

Log in to the managed server as the installation user and execute the following command to back up the information related to the management platform (ycm-agent).

```shell
$ /opt/ycm/ycm-agent/scripts/backup.sh -n ycm-agent -i /opt/ycm/ycm-agent
```

### Restore

#### Precautions

- Before restoring, you need to obtain the storage path of the backup file.

- After the restore is completed, the related service processes will automatically restart, and **ycm-agent will be unavailable during this time**, so please arrange the operation time reasonably.

#### Operation Steps

Log in to the managed server as the installation user and execute the following command to restore the information related to the management platform (ycm-agent).

```shell
$ /opt/ycm/ycm-agent/scripts/restore.sh -n ycm-agent -i /opt/ycm/ycm-agent -p /opt/ycm/ycm-agent/backup/ycm-agent-20231221-152809.tar.gz
```
