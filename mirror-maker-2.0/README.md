**测试环境准备**

```shell
# kafka 下载
wget https://archive.apache.org/dist/kafka/3.0.0/kafka_2.13-3.0.0.tgz
# 解压
tar zxvf kafka_2.13-3.0.0.tgz
cd kafka_2.13-3.0.0
```

```shell
export PATH="/Users/lizexin/application/kafka/kafka_2.13-3.0.0/bin:$PATH"
kafka_server=127.0.0.1:9092
topic="testA"
topic="exclude_testA"
```

```shell
export PATH="/Users/lizexin/application/kafka/kafka_2.13-3.0.0/bin:$PATH"
kafka_server=127.0.0.1:9192
topic="A.testA"
topic="A.exclude_testA"
```

**启动MM2**

```shell
connect-mirror-maker.sh connect-mirror-maker.properties
```

**Topic 相关操作**

```shell
# 删除topic
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic $topic

# 删除A集群中mm2相关的topic
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic heartbeats
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-configs.B.internal
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-offsets.B.internal
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-status.B.internal

# 删除B集群中mm2相关的topic
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic A.checkpoints.internal
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic A.heartbeats
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic A.testA
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic heartbeats
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-configs.A.internal
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-offset-syncs.A.internal
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-offsets.A.internal
kafka-topics.sh --bootstrap-server $kafka_server --delete --topic mm2-status.A.internal

# 创建Topic
kafka-topics.sh --bootstrap-server $kafka_server --create --replication-factor 3 --partitions 10 --topic $topic

# topic详情
kafka-topics.sh --bootstrap-server $kafka_server --describe --topic $topic

# 修改 topic partition
kafka-topics.sh --bootstrap-server $kafka_server --alter --partitions 11 --topic $topic

# 列出所有topic
kafka-topics.sh --bootstrap-server $kafka_server --list

# 获取topic消息量
kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list $kafka_server --topic $topic --time -1
```

**Producer 相关**

```shell
# 发布消息
kafka-console-producer.sh --broker-list $kafka_server --topic $topic
```

**Consumer 相关**

```shell
# 订阅消息
kafka-console-consumer.sh --bootstrap-server $kafka_server --group consumer_01 --topic $topic
kafka-console-consumer.sh --bootstrap-server $kafka_server --group consumer_02 --from-beginning --topic $topic
kafka-console-consumer.sh --bootstrap-server $kafka_server --group consumer_03 --from-beginning --topic $topic

# 获取所有group
kafka-consumer-groups.sh --bootstrap-server $kafka_server --list

# 查看group详细信息
kafka-consumer-groups.sh --bootstrap-server $kafka_server --describe --group consumer_01
kafka-consumer-groups.sh --bootstrap-server $kafka_server --describe --group consumer_02
```