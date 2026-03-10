If the management platform is temporarily not needed, you can manually stop the management platform. If a server temporarily does not need to be hosted, you can manually stop the ycm-agent service on that server.

After manually stopping the relevant services, if you need to resume usage, you can only do so by manually starting them.

>**Caution**:
>
> - **Manual service stop operations are not recommended.**
> - Under normal circumstances, after terminating the relevant service processes, the monit process will automatically restart them in a timely manner. Therefore, it is not possible to stop the platform and ycm-agent services by terminating the processes.

## Manually Stopping the Platform

Log in to the server where the platform is installed as the installation user and execute the following commands to stop the platform-related services.

```bash
# /opt/ycm is an example installation path for the management platform, please use the actual path
$ /opt/ycm/ycm/monit/monitctl run stop -g ycm
$ /opt/ycm/ycm/monit/monitctl run stop -g prometheus
$ /opt/ycm/ycm/monit/monitctl run stop -g loki
$ /opt/ycm/ycm/monit/monitctl run stop -g promtail
$ /opt/ycm/ycm/monit/monitctl run stop -g yashandb_exporter
$ /opt/ycm/ycm/monit/monitctl stop
```

## Manually Stopping ycm-agent

Log in to the target server that does not need to be hosted temporarily as the installation user, and execute the following commands to stop the ycm-agent related services.

```bash
# /opt/ycm is an example installation path for the ycm-agent, please use the actual path
$ /opt/ycm/ycm-agent/monit/monitctl run stop -g ycm-agent
$ /opt/ycm/ycm-agent/monit/monitctl run stop -g node_exporter
$ /opt/ycm/ycm-agent/monit/monitctl run stop -g promtail
$ /opt/ycm/ycm-agent/monit/monitctl stop
```

## Manually Starting the Platform

Log in to the server where the platform is installed as the installation user and execute the following commands to start the platform services.

```bash
# /opt/ycm is an example installation path for the management platform, please use the actual path
$ /opt/ycm/ycm/monit/monitctl start
$ /opt/ycm/ycm/monit/monitctl run start -g ycm
$ /opt/ycm/ycm/monit/monitctl run start -g prometheus
$ /opt/ycm/ycm/monit/monitctl run start -g loki
$ /opt/ycm/ycm/monit/monitctl run start -g promtail
$ /opt/ycm/ycm/monit/monitctl run start -g yashandb_exporter
```

## Manually Starting ycm-agent

Log in to the hosted target server as the installation user and execute the following commands to start the ycm-agent related services.

```bash
# /opt/ycm is an example installation path for the ycm-agent, please use the actual path
$ /opt/ycm/ycm-agent/monit/monitctl start
$ /opt/ycm/ycm-agent/monit/monitctl run start -g ycm-agent
$ /opt/ycm/ycm-agent/monit/monitctl run start -g node_exporter
$ /opt/ycm/ycm-agent/monit/monitctl run start -g promtail
```

## Manually Restarting the Platform

Log in to the server where the platform is installed as the installation user and execute the following commands to restart the platform-related services.

```bash
# /opt/ycm is an example installation path for the management platform, please use the actual path
$ /opt/ycm/ycm/monit/monitctl run restart -g ycm
$ /opt/ycm/ycm/monit/monitctl run restart -g prometheus
$ /opt/ycm/ycm/monit/monitctl run restart -g loki
$ /opt/ycm/ycm/monit/monitctl run restart -g promtail
$ /opt/ycm/ycm/monit/monitctl run restart -g yashandb_exporter
$ /opt/ycm/ycm/monit/monitctl restart
```

## Manually Restarting ycm-agent

Log in to the hosted target server as the installation user and execute the following commands to restart the ycm-agent related services.

```bash
# /opt/ycm is an example installation path for the ycm-agent, please use the actual path
$ /opt/ycm/ycm-agent/monit/monitctl run restart -g ycm-agent
$ /opt/ycm/ycm-agent/monit/monitctl run restart -g node_exporter
$ /opt/ycm/ycm-agent/monit/monitctl run restart -g promtail
$ /opt/ycm/ycm-agent/monit/monitctl restart
```

> **Note**:
>
> If the deployment configuration of the management platform is high availability, you need to perform start and stop operations on each instance host.

## Whitespace Restart ycm-agent

On the host list page, click the **[ Restart ]** button in the operation column, which can restart the ycm-agent related services on the host. Depending on whether the ycm-agent process is online, there are the following two ways to handle it:

- If the ycm-agent process is online, restart itself and related services by connecting to the host ycm-agent via RPC.
- If the ycm-agent process is not online, restart the ycm-agent related services by connecting to the host via SSH, which requires providing the SSH user password.