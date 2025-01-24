#  体系结构

## 1. Producer

生产者，写入消息

## 2. Consumer

消费者，读取消息

## 3. ConsumerGroup

消费者组，一个消费者组包含多个消费者，可以共同读取一个或多个主题中的消息。

- 一个 消息只能由一个消费者读取，一个消费者可以读取多个消息

```C
//创建消费者组(创建主题后指定)
kafka-consumer-groups.sh --bootstrap-server master:9092 --topic meng --group meng

//查看已创建的消费者组
kafka-consumer-groups.sh --bootstrap-server master:9092 --list

//创建一个消费者组多个消费者:一个终端执行一个命令(消费者组相同)就是一个消费者
kafka-concole-consumer.sh --bootstrap-server master:9092 --topic meng --group mengxiaoqi

//删除消费者组(会删除所有信息包括消费者)
bin/kafka-consumer-groups.sh --bootstrap-server master:9092 --delete --group mengxiaoqi
```

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1728387733488-e2e6c407-2b28-4a59-8c9d-b186045c8476.png)

- Broker:服务代理节点，可以看作一台 kafka 服务器，broker 会接受来自生产者的消息，为消费者提供服务

# 重要概念

- 消息：包括数据内容及元数据信息
- Topic:主题，相当于文件系统中的文件夹或者是数据库中的表。
- Partition:分区，一个主题可以有多个分区，一个消息会被写入到某一个分区中
- offset:偏移量，消息在分区中的唯一标识
- replication:副本，同一分区的不同副本保存相同信息，副本之间时‘’一主多从“ 关系，leader 副本负责处理读写请求，follower 副本负责与 leader 副本的消息同步
- 序列化：将对象的状态信息转换为可以存储或传输的形式

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1728222667600-e0f89748-163e-4630-90b9-9a9b61e3c008.png)

有界数据：历史数据（数据库数据、文件）

无界数据：实时数据（kafka）

乱序事件：事件生成的时间与到达系统的时间不一致（网络延迟）

时间戳

- 事件时间：事件生成的时间（由用户行为决定，比如点击购买按钮）。可以处理乱序事件
- 处理时间：事件到达系统的事件

水印策略：用来处理事件时间，处理流数据的乱序问题

- 固定水印：在允许的延迟时间内处理时间
- 周期性水印：定期生成水印，用于在事件时间分布不均匀（前后两段时间的数据相差大）
- 无序水印：用于在事件时间不重要



# 命令

- 如果 该 kafka 版本命令参数中有“bootstrap-server”,优先使用此参数

```c++
//启动kafka
kafka-server-start.sh -daemon $KAFKA_HOME/config/server.properties

//创建主题
1.kafka-topics.sh --create \
  --zookeeper <主机名:2181> \  //指定 Zookeeper 的地址和端口
  --topic <topic_name> \
  --partitions <num_partitions> \
  --replication-factor <num_replicas>
     
2.kafka-topics.sh --create \
  --bootstrap-server <主机名:9092> \  //指定 kafka 的地址和端口
  --topic <topic_name> \
  --partitions <num_partitions> \
  --replication-factor <num_replicas>

//列出所有主题
1.kafka-topics.sh --list --zookeeper <主机名:2181>
2.kafka-topics.sh --list --bootstrap-server <主机名:9092>

//查看主题详情
1.kafka-topics.sh --describe --zookeeper <主机名:2181> --topic <topic_name>
2.kafka-topics.sh --describe --bootstrap-server <主机名:9092> --topic <topic_name>

//删除主题
1.kafka-topics.sh --delete --zookeeper <主机名:2181> --topic <topic_name>
2.kafka-topics.sh --delete --bootstrap-server <主机名:9092> --topic <topic_name>

//启动生产者
1.kafka-console-producer.sh --broker-list <主机名:9092> --topic <topic_name>
2.kafka-console-producer.sh  --bootstrap-server <主机名:9092> --topic <topic_name>
   
//启动消费者
1.kafka-console-consumer.sh --zookeeper <主机名:2181> --topic <topic_name>
2.kafka-console-consumer.sh --bootstrap-server <主机名:9092> --topic <topic_name> \
   --from-beginning  //从头开始
   --max-messages <number>  //最大消息数量
```

