To improve business isolation and reduce operational errors, it is recommended to create an independent installation user for the YashanDB product. The following operations must be performed on all servers.

1. Create installation user.

    In this installation manual, the database installation user name is set as yashan.

    ```shell
    # useradd -d /home/yashan -m yashan
    # passwd yashan
    ```

2. Create a new YASDBA user group and add the installation user to this group.

    ```shell
    # groupadd YASDBA
    # usermod -a -G YASDBA yashan
    ```
