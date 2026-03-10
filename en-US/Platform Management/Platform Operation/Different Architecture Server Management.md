The management platform by default can only add hosts with the same CPU architecture. 

For instance, if the management platform is deployed on x86_64, it can only add x86_64 hosts by default. If you want to manage aarch64 hosts simultaneously, additional actions are required. Taking the example of deploying the management platform on an x86_64 host and adding an aarch64 host (and vice versa):

1. Prepare both the x86_64 and aarch64 management platform installation packages.

2. Follow the installation and deployment steps above to complete the management platform deployment on the x86 server using the x86_64 installation package, with the installation directory set to `/opt/ycm`.

3. Execute the command to add the aarch64 installation package.

    ```bash
    cd /opt/ycm/ycm
    bash ./scripts/plugin.sh add <path to management platform aarch64 installation package>
    ```
4. Follow the [Server Management](../Resource Management/Server Management) operations to add the aarch64 host.

> **Note**：
>
> If the management platform is deployed in a high availability configuration, the command to add the aarch64 installation package needs to be executed on the server of each node.