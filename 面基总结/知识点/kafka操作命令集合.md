## 配置与启动

```
2. 启动内置的zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties
1. 启动kafka服务
bin/kafka-server-start.sh config/server.properties
3.创建topic话题
bin/kafka-topics.sh --create --topic test --bootstrap-server 192.168.218.128:9092 -partitions 3 -replication-factor 1
查看所有topic话题
bin/kafka-topics.sh --list --bootstrap-server 192.168.218.128:9092
查看指定话题的详情
bin/kafka-topics.sh --bootstrap-server 192.168.230.128:9092 --describe --topic test
3.创建生产者
bin/kafka-console-producer.sh --broker-list 192.168.218.128:9092 --topic test
4.创建消费者
bin/kafka-console-consumer.sh --bootstrap-server 192.168.218.128:9092 --topic test
```



## 管理

```
## 创建主题（4个分区，2个副本）
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 4 --topic test
```

## 查询

```
## 查询集群描述
bin/kafka-topics.sh --describe --zookeeper 

## topic列表查询
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list

## topic列表查询（支持0.9版本+）
bin/kafka-topics.sh --list --bootstrap-server localhost:9092

## 新消费者列表查询（支持0.9版本+）
bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server localhost:9092 --list

## 新消费者列表查询（支持0.10版本+）
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list

## 显示某个消费组的消费详情（仅支持offset存储在zookeeper上的）
bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --zookeeper localhost:2181 --group test

## 显示某个消费组的消费详情（0.9版本 - 0.10.1.0 之前）
bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server localhost:9092 --describe --group test-consumer-group

## 显示某个消费组的消费详情（0.10.1.0版本+）
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-group
```

## 发送和消费

```
## 生产者
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

## 消费者
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test

## 新生产者（支持0.9版本+）
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test --producer.config config/producer.properties

## 新消费者（支持0.9版本+）
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --new-consumer --from-beginning --consumer.config config/consumer.properties

## 高级点的用法
bin/kafka-simple-consumer-shell.sh --brist localhost:9092 --topic test --partition 0 --offset 1234  --max-messages 10
```

## 平衡leader

```
bin/kafka-preferred-replica-election.sh --zookeeper zk_host:port/chroot
```

## kafka自带压测命令

```
bin/kafka-producer-perf-test.sh --topic test --num-records 100 --record-size 1 --throughput 100  --producer-props bootstrap.servers=localhost:9092

```

## 分区扩容

```
bin/kafka-producer-perf-test.sh --topic test --num-records 100 --record-size 1 --throughput 100  --producer-props bootstrap.servers=localhost:9092
```

## 迁移分区

1. 创建规则json

   ```
   cat > increase-replication-factor.json <<EOF
   {"version":1, "partitions":[
   {"topic":"__consumer_offsets","partition":0,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":1,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":2,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":3,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":4,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":5,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":6,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":7,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":8,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":9,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":10,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":11,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":12,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":13,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":14,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":15,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":16,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":17,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":18,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":19,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":20,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":21,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":22,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":23,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":24,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":25,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":26,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":27,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":28,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":29,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":30,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":31,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":32,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":33,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":34,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":35,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":36,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":37,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":38,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":39,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":40,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":41,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":42,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":43,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":44,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":45,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":46,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":47,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":48,"replicas":[0,1]},
   {"topic":"__consumer_offsets","partition":49,"replicas":[0,1]}]
   }
   EOF
   ```

   

2. 执行

   ```
   bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --execute
   ```

   

3. 验证

   ```
   bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --verify
   ```

   

