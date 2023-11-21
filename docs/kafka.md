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

### 安装

```docker
docker volume create kafka-data
docker run -d --name kafka-server \
  --network host \
  -v kafka-data:/opt/kafka/data \
  --env KAFKA_CONTROLLER_LISTENER_PORT=29091 \
  --env KAFKA_BROKER_LISTENER_PORT=29092 \
  docker.nju.edu.cn/sir5kong/kafka
```

### 延迟消息

* 时间轮和delay-file实现

<https://gitee.com/venus-suite/rocketmq-with-delivery-time/wikis/2.%E6%97%B6%E9%97%B4%E8%BD%AE%E5%92%8Cdelay-file%E5%AE%9E%E7%8E%B0>
