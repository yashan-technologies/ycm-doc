If you want to uninstall the management platform, you can follow these steps for cleanup:

1. Remove the managed database.
   
    - Server-side removal: Use the `yasadm yasdb remove` command of [yasadm](../../Reference/Tools Introdution/yasadm.html#remove) to unmanage the database.
    
    - Web-side removal: Refer to [Remove Database](../../Platform Management/Resource Management/Database Management.html#remove) to remove the database from the management platform.
    
2. Remove the server: Refer to [Remove Server](../../Platform Management/Resource Management/Server Management.html#remove) to remove the server from the management platform.

3. Go to the installation path and execute the uninstall command.

    ```bash
    $ cd /opt/ycm/ycm
    $ sudo ./scripts/clean.sh 
    # Confirm whether to uninstall based on the prompt, input yes if uninstallation is necessary.
    ```
> **Note**：
>
> If in primary/standby deployment mode, the backend metadata of this node will also be deleted during uninstallation. If deletion fails during the uninstallation process, please manually remove it using the `yasadm instance delete` command of [yasadm](../../Reference/Tools Introdution/yasadm.html#remove) on other nodes.

4. Clean up residual files.
   ```bash
   $ sudo rm -rf /opt/ycm
   ```
