
* 官方文档

<https://docs.starrocks.io/zh-cn/latest/loading/RoutineLoad>

```bash
SHOW ROUTINE LOAD;

SHOW ROUTINE LOAD TASK where JobName  = 'jobName';

SHOW ROUTINE LOAD FOR jobName;

RESUME ROUTINE LOAD FOR jobName;

STOP ROUTINE LOAD FOR jobName;
```

* 导入作业

```
CREATE ROUTINE LOAD example_db.example_tbl2_ordertest2 ON example_tbl2
COLUMNS(commodity_id, customer_name, country, pay_time, price, pay_dt=from_unixtime(pay_time, '%Y%m%d'))
PROPERTIES
(
    "desired_concurrent_number"="5",
    "format" ="json",
    "jsonpaths" ="[\"$.commodity_id\",\"$.customer_name\",\"$.country\",\"$.pay_time\",\"$.price\"]"
 )
FROM KAFKA
(
    "kafka_broker_list" ="<kafka_broker1_ip>:<kafka_broker1_port>,<kafka_broker2_ip>:<kafka_broker2_port>",
    "kafka_topic" = "ordertest2",
    "kafka_partitions" ="0",
    "property.kafka_default_offsets" = "OFFSET_BEGINNING"
);
```

* 遇到问题

```bash
1064 - failed to send proxy request to TNetworkAddress(hostname:XXX, port:8060) err failed to send proxy request to TNetworkAddress(hostname:XXX, port:8060) err [no partition in this topic]

检查kafka topic是否存在,重建topic
```
