### topic

* create

```bash
./kafka-topics.sh --bootstrap-server 192.168.0.100:9092 --create --replication-factor 1 --partitions 1 --topic topicName
```

* delete

```bash
./bin/kafka-topics --bootstrap-server 192.168.0.100:9092 --delete --topic topicName
```

* detail

```bash
./bin/kafka-topics.sh --bootstrap-server 192.168.0.100:9092 --describe --topic topicName
```
