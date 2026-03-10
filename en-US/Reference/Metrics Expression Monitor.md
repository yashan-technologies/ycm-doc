To create monitoring indicators, users need to be familiar with YashanDB and [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) syntax.

Once created successfully, you can click **[ Check ]** to find out if the expression is written correctly. If the expression cannot be configured in the monitoring dashboard, it will display in the corresponding chart: "No data available, please check the specific expression of this monitoring indicator and the chart configuration."

## Host

For scenarios where the indicator type is host, you can refer to the preset indicators for processing.

For example, the network throughput (transmit) indicator is used to count the network throughput of each host, with the expression `irate(node_network_transmit_bytes_total[5m])*8`.

To count the host with the maximum network throughput among all hosts, you can write the expression with PromQL syntax as `max by (nodeIp) (irate(node_network_transmit_bytes_total[5m])*8)`.

## Database

For scenarios where the indicator type is YashanDB, you can still refer to the preset indicators for processing.

You can also consult the file ./etc/yasexporter.metrics.yml in the management platform installation directory, where the name is the collection indicator name. Then you can process the collection indicator name to write expressions.

```shell
  - name: long_transactions
    query: select count(*) as long_transactions from v$transaction where TIMESTAMPDIFF(MINUTE,START_DATE,sysdate)>3;
    de_query:
      SELECT CONCAT(group_id,'-',group_node_id) AS node_id,COUNT(*) AS long_transactions
      FROM dv$transaction WHERE TIMESTAMPDIFF(MINUTE,START_DATE,SYSDATE)>3 GROUP BY GROUP_ID, GROUP_NODE_ID
    sub_metrics:
      - col: long_transactions
        TYPE: gauge
        description: TIME cost OF TRANSACTION
  ```

For example, to establish an indicator that monitors the nodes with the least operations executed per second in each database, you can write the expression using PromQL syntax as `min by (yasdbName) (irate(yashandb_operations[1m]))`.