When the backend database of the platform is selected as YashanDB, directly modifying the password of the backend database user may lead to the user being locked out. **Please follow the instructions in this document for modifications.**

## Notes

Changing the password of the backend database user requires stopping the service process. **During this time, the management platform will be unavailable**, so please arrange the operation time appropriately.

## Operation Steps

1. Log in to each server of the management platform as the installation user, and execute the following command to stop the platform service ycm process.

    ```shell
    # /opt/ycm is an example of the management platform installation path, please use the actual path
    $ /opt/ycm/ycm/monit/monitctl run stop -g ycm
    ```

2. Log in to the server of the backend database as the database installation user, connect to the backend database, and execute the following SQL statement to change the password of the backend database user.

    ```shell
    # yasman/password@192.168.1.2:1688 is an example of the backend database connection information, please use the actual connection information
    $ yasql yasman/password@192.168.1.2:1688
    SQL> ALTER USER yasman IDENTIFIED BY "new_password";
    SQL> exit
    ```

3. On each server of the platform, use the [yasadm](../../Reference/Tools Introdution/yasadm) tool to modify the encrypted password in the ycm.yaml configuration file.

    ```shell
    # /opt/ycm is an example of the management platform installation path, please use the actual path
    $ /opt/ycm/ycm/scripts/yasadm password reset -n new_password -t backend
    ```

4. On each server of the platform, execute the following command to start the platform service ycm process.

    ```shell
    # /opt/ycm is an example of the management platform installation path, please use the actual path
    $ /opt/ycm/ycm/monit/monitctl run start -g ycm
    ```
