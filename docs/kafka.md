### topic

* create

```
./kafka-topics.sh --bootstrap-server 192.168.0.100:9092 --create --replication-factor 1 --partitions 1 --topic topicName
```

* delete

```
./bin/kafka-topics --bootstrap-server 192.168.0.100:9092 --delete --topic topicName
```

* detail

```
./bin/kafka-topics.sh --bootstrap-server 192.168.0.100:9092 --describe --topic topicName
```
