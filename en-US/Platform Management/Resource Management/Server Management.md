**Web Path**: **[ Host Management ]**>**[ Host list ]**

## Host Requirements

The management platform has the following configuration requirements for the servers (hosts) that are included in the takeover:

- CPU Cores: 2 cores or more
- Memory: 4G or more
- Agent installation path space: 1G or more

> **Caution**：
>
> When the server specifications do not meet the above requirements, the host cannot be added.

## Add Host

**Web Path**: **[ Add host ]**

**Functionality Introduction**

The management platform supports the unified takeover and monitoring of YashanDB servers. The steps to add a host are as follows:

1. After filling in the host information, click **[ Scan ]** to display whether the host is connected successfully. If the connection fails, you can click **[ Edit ]** to modify the username and password, or **[ Clear Failed Hosts ]**.

2. Click **[ Next ]**; if the host check is successful, you can modify information such as port and Agent installation path as needed. If the check fails, you can refer to [Host Scanning Issues](../../FAQ/Host Management).
3. Click **[ Submit ]**, and the host will be successfully added and redirected to the host list.

**Main Content Explanation**

**[ Hostip ]**: Required parameter. The host IP address must comply with the following rules:

- When adding multiple hosts, different IP addresses can be entered on separate lines using the Enter key.
- The format for entering address ranges: 192.168.x.[x-x]. For example: 192.168.30.[5-30] indicates adding and scanning all hosts between 192.168.30.5 and 192.168.30.30.

**[ Username ]**: Required parameter. If the user is not root or does not have sudoPrivilege, please ensure the user is consistent with the management platform system user adding the host and has joined the YASDBA user group. If multiple users are added, it must be a root or a user with sudoPrivilege.

**[ Password ]**: Required parameter, used only for establishing an SSH connection when creating a database instance.

**[ SSH Port ]**: Required parameter, default value is 22.

**[ Management Platform System User ]**: Required parameter. If multiple users are filled in, please separate them with semicolons, where the first user is the installation user of the host.

> **Note**：
>
> When managing multiple users on the host, the pam and facl components on the host will be used to configure the management privileges for other users. Please ensure that these components are present on the host.


**[ Port ]**:
<span name="user" id="user" class="yaslink"></span>

- Management Platform Agent User: The system user name for managing hosts, subsequent hosts will use this user for operations, default is yashan (if database management is required later, it should be consistent with the database system user).
- Management Platform Agent User Group: The user group to which the system user belongs, default is yashan.
- Management Platform Agent Port: The port for communication between ycm-agent and the management platform, and between ycm-agent and the server where it is located, default is 9070.
- Host Information Collection Port: The port for monitoring and collecting various information from the host, default is 9071.
- Process Information Collection Port: The port for monitoring and collecting various information about processes, default is 9073.

**[ Installation Path ]**: The install path for the agent service on the management host. This input box has a default value of /opt/ycm, which can be modified as needed (if **[ Username ]** is not root or does not have sudoPrivilege, please ensure the user has privilege for that path).

## Update

**Web Path**: **[ Update ]**

**Functionality Introduction**

When the basic information specifications of the host CPU, memory, disk, etc. change, you can click **[ Update ]** to view the latest host details.

## Subscribe Host

**Web Path**: **[ Subscribe ]**

**Web Path**: **[ Hostname ]**>**[ Subscribe ]**

**Functionality Introduction**

After subscribing to the host, the management platform can push a series of messages (alarm messages, resource changes, task messages) for that user, which can be viewed in the user's personal center.

## Add User

**Web Path**: **[ Add user ]**

**Functionality Introduction**

Add a new server user for host management; after addition is complete, the user can manage the databases on that user.

<span name="remove" id="remove" class="yaslink"></span>

## Remove Host

**Web Path**: **[ Remove ]**

**Web Path**: **[ Hostname ]**>**[ Remove ]**

**Functionality Introduction**

Remove the host from the management platform.

> **Note**：
>
> If there are hosted databases on the host, it cannot be removed.


## Scan Network

**Web Path**: **[ Hostname ]**>**[ Scan Network ]**

**Functionality Introduction**

In case of changes in the host network card information (such as a network card failure causing an IP to become unusable), clicking **[ Scan Network ]** will update the IP information of that network saved by the management platform.

> **Note**:
>
> Scanning the network may encounter exceptions, namely when the IP already in use by the database deployed on the management platform does not exist on the actual host, the management platform will prompt to check the host network information before performing network scanning. Re-hosting can also be performed.

## Restart

**Web Path**: **[ Restart ]**

**Functionality Introduction**

Restart the ycm-agent related services on the host. Based on whether the ycm-agent process is online, there are two restart methods:

- If the ycm-agent process is online, restart itself and related services by connecting to the host ycm-agent through rpc.
- If the ycm-agent process is not online, restart ycm-agent related services by connecting to the host via ssh, this method requires providing the ssh user password.

## Reinstall

**Web Path**: **[ Reinstall ]**

**Functionality Introduction**

Reinstall the ycm-agent related services on the host. Based on whether the ycm-agent process is online, there are two reinstall methods:

- If the ycm-agent process is online, reinstall itself and related services by connecting to the host ycm-agent through rpc.
- If the ycm-agent process is not online, reinstall ycm-agent related services by connecting to the host via ssh, this method requires providing the ssh user password.

## BMC Management Configuration

**Web Path**: **[ Hostname ]** > **[ Hardware Configuration ]**

**Functionality Introduction**

Adding BMC host information allows displaying detailed hardware information of the BMC host, such as CPU, memory, storage, network interface cards, power supplies, and fans.
