**Web Path**: **[ YashanDB ]**>**[ YashanDB List ]**>**[ DB Name ]**>**[ Failover ]**

![](./image/failover.png)

## Standalone Deployment Database

**Functionality Introduction**

When auto-election is enabled, if the primary database fails, the available standby database will automatically be promoted to primary.

When auto-election is disabled, if the primary database fails, use failover to select an available standby database to promote it to primary.

> **Note**：
>
> After promoting a standby database to primary using failover, if the primary database recovers, it should be manually demoted in a timely manner. **Do not directly OPEN**, otherwise it may lead to a dual-primary abnormal scenario.

## Distributed Deployment Database

**Functionality Introduction**

The Distributed Deployment database requires a highly available deployment environment.

The distributed failover functions the same as the standalone functionality. The page displays an additional instance group compared to standalone, allowing the selection of a specific MN or DN group instance for failover.