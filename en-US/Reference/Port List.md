The main ports used by the management platform server and the platform-hosted servers are shown below. The default values for these port numbers are the initial values in the configuration file.

## Management Platform Server

|Service |Port Number |Description |Port Open Rules |
| ----- | ----- | ----- | ----- |
| Management Platform Service | Default: 9060<br/>Range: [1024,65535] | Provides management platform services to the outside | Must be open to browser hosts accessing the management platform; open to other management platform servers in high availability scenarios |
| Monitoring and Warning Component | Default: 9061<br/>Range: [1024,65535] | Internal communication port for monitoring data queries | Local access |
| Log Collection Component | Default: 9062, 9063<br/>Range: [1024,65535] | Internal communication port for log collection and queries | Must be open to managed servers |
| Database Metric Collection Component | Default: 9064<br/>Range: [1024,65535] | Internal communication port for database metric collection | Local access |

## Platform-Hosted Servers

|Service |Port Number |Description |Port Open Rules |
| ----- | ----- | ----- | ----- |
| Management Platform Agent Service | Default: 9070<br/>Range: [1024,65535] | Responds to RPC calls from the management platform | Must be open to the management platform and other managed servers |
| Host Information Collection Component | Default: 9071<br/>Range: [1024,65535] | Collects host information, such as CPU, memory, disk I/O, etc. | Must be open to the management platform |

## Platform-Hosted Databases

|Service |Port Number |Description |Port Open Rules |
| ----- | ----- | ----- | ----- |
| Database Listener | Standalone-default: 1688<br/>Distributed-default: 1678, 1688, 1699<br/>YAC-default: 1688<br/>Range: [1024,65535] | Database listening port | Must be open to the management platform |
| yasom | Default: 1675<br/>Range: [1024,65535] | Used to receive and respond to yasboot commands | Must be open to the management platform |
| yasagent | Default: 1676<br/>Range: [1024,65535] | Used to receive and respond to yasom instructions | Must be open to the management platform |