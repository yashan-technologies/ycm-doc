## Open Ports

Running YashanDB requires a series of ports. YashanDB has established a set of port allocation rules and provides default values for port numbers that need to be specified during the installation and deployment process. Users may modify the port numbers according to their own network planning, but they should adhere to the port allocation rules to avoid port conflicts.

The port allocation rules and default values are as follows:

- The database listening port is specified by the `beging-port` / starting port parameter during the installation steps, with a default value of 1688.

- The database listening port will serve as the initial value used to calculate other internal communication ports.

    - For Standalone Deployment, if the syntax mode needs to be specified as MySQL mode, a set of listening ports for MySQL protocol must also be planned, with a default value of 1690. Each standby database defaults to +3, and so on.

    - In Distributed Deployment, port numbers are generated in the order of MN, CN, and DN, with internal communication ports generated in the order of inter-group communication and intra-group communication. When deploying multiple DN groups, each DN group defaults to +3, and so on.

    |Deployment Type |Database Listening |Internal Communication |yasom |yasagent |
    | ------------ | ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------- | ----------------------- |
    | Standalone Deployment | yashan mode: 1688 <br/> mysql mode: 1688 and 1690, where 1690 is the default listening port for MySQL protocol | replication link (required for primary/standby deployment): initial value +1, defaulting to 1689                                                                                                                | Initial value -13, defaulting to 1675 | Initial value -12, defaulting to 1676 |
    | YAC Deployment       | 1688                                                                                          | * Communication between database instances in the same cluster: initial value +1, defaulting to 1689 <br/> * Communication between YCS instances: initial value +100, defaulting to 1788 <br/> * replication link (required for primary/standby deployment): initial value +2, defaulting to 1690 | Initial value -13, defaulting to 1675 | Initial value -12, defaulting to 1676 |
    | Distributed Deployment| MN: initial value -10, defaulting to 1678 <br/> CN: 1688 <br/> DN: initial value +10, defaulting to 1698 | Corresponding listening ports incremented by +1, generated in the order of inter-group communication and intra-group communication <br/><br/> * Inter-group communication: MN is 1679, CN is 1689, DN is 1699 <br/> * Intra-group communication: MN is 1680, CN is 1690, DN is 1700             | Initial value -13, defaulting to 1675 | Initial value -12, defaulting to 1676 |

- For servers that need to enable the visual deployment web service, port 9001 must also be used.

After completing the port planning, the corresponding ports must be opened on **all servers**.

### Method 1: Disable the Firewall

Execute the following commands on all servers to disable the firewall:

```shell
## Disable the firewall
# systemctl stop firewalld 
## Disable automatic startup
# systemctl disable firewalld
```

### Method 2: Add to Whitelist

If the firewall cannot be turned off, the following steps must be taken to add the ports to the whitelist:

1. Check the firewall port opening status:

    ```shell
    # firewall-cmd --zone=public --list-ports
    ```

2. Add ports to the firewall:

    Using 1688 as an example to demonstrate how to add a port to the firewall. The method for other ports is the same.

    ```shell
    ## Add (--permanent means permanent effect; without this parameter it will be invalid after restart)
    # firewall-cmd --zone=public --add-port=1688/tcp --permanent
    ## Reload
    # firewall-cmd --reload
    ## Check
    # firewall-cmd --zone=public --query-port=1688/tcp
    ```

    > **Note**:
    >
    > Opening ports through the whitelist may still pose risks of deployment failure due to communication issues with the ports during installation. In this case, contact the company's network administrator to clarify the reason, and reopen the ports before proceeding with the installation.

3. To remove an already added port from the whitelist, use the following command:

    ```shell
    # firewall-cmd --zone=public --remove-port=1688/tcp --permanent
    ```

## Start SSH Service

Check whether the SSH service is enabled on all servers:

```shell
# systemctl status sshd.service
```

If the output does not contain `active (running)`, please refer to the following command to start the SSH service:

```shell
# systemctl start sshd.service
```

> **Note**：
>
> This installation manual assumes the default SSH port number is 22. If it is planned to be another port number, it will need to be manually specified during the subsequent installation process.

## Clean Environment (YAC Deployment)

If deploying a YashanDB YAC, all servers need to clear shared memory.

```shell
# ipcrm -a
```
