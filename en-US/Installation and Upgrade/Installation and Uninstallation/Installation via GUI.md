Visualization deployment is only supported when the backend database is SQLite.

## Step 1: Start the web service

Use sudo to execute the command to start the web service.

```bash
sudo ./ycm-init web --addr 0.0.0.0:9001
```

Parameters are explained as follows:

|Parameter |Explanation |
| ------ | ------------------------------------------- |
| --addr    | Default value is "0.0.0.0:9001", starts the initialized Web service, providing API interface and front-end page |

## Step 2: Modify Configuration

Open the browser and visit IP:9001. Modify the selection options on the page as needed, and then click **[ Initialize ]**.

**Parameter Explanation**

|Parameter |Explanation |
| ------------- | -------------------------------- |
| Management Platform System Username | User running the management platform server related services, default value is yashan  |
| Management Platform Service Installation Path | Installation path of the management platform server, default value is /opt/ycm    |
| Management Platform Service Port | Default value is 9060                          |
| Monitoring Service Port       | Default value is 9061                           |
| Loki HTTP Service Port        | Log analysis HTTP service port, default value is 9062            |
| Loki gRPC Service Port        | Log analysis gRPC service port, default value is 9063            |
| Yasdb Collector Port          | Database information collection port, default value is 9064      |
| Monitoring Data Collection Frequency | Collection frequency of management platform monitoring, valid range is [1,43200] |
| Monitoring Data Retention Time | Days to retain monitoring data in the management platform, valid range is [1,360]  |
| Host System Operating Username  | Default running user for ycm-agent, applied when adding hosts   |
| Management Platform Agent Service Port | Default port for ycm-agent, default value is 9070    |
| Collection Service Port        | Default port for Prometheus exporter, default value is 9071  |
| Management Platform Agent Installation Path | Default installation path for ycm-agent  |
| Password                       | Default password for super administrator user is admin          |
| Name                           | Default value is surperman                              |
| Email                          | Optional                                               |
| Phone Number                   | Optional                                               |

**Deploying HTTPS**

The visualization deployment defaults to using the HTTP protocol. If HTTPS protocol access is required, please refer to the installation and deployment (command line) for manual configuration.

## Step 3: Access Management Platform

Once deployment is complete, open the browser to visit `HTTP(S)://IP:9060` to use the management platform service.

![](./image/ycm.png)
