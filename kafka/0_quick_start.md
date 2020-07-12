# 一、环境搭建
准备搭建一个3节点kafka集群。     
使用版本：`kafka_2.12-2.5.0.tgz`   
修改配置文件：`server.properties`
``` properties
broker.id=0
log.dirs=/opt/module/kafka/logs
zookeeper.connect=CentOS1:2181,CentOS2:2181,CentOS2:2181
```
其中`broker.id`是每个结点的唯一ID，每个结点设置的都不同，这里分别设置了0,1,2
# 二、命令行API
## 启动结点
`kafka-server-start.sh -daemon server.properties`
## 关闭结点
`kafka-server-stop.sh stop`
## 创建topic
``` shell
[root@CentOS1 module]# kafka-topics.sh --zookeeper CentOS1:2181 --create --replication-factor 3 --partitions 1 --topic config
Created topic config.
```
## 查看topic
``` shell
kafka-topics.sh --zookeeper CentOS1:2181 --list
config
```
## 删除topic
``` shell
kafka-topics.sh --zookeeper CentOS2:2181 --delete --topic config
Topic config is marked for deletion.
Note: This will have no impact if delete.topic.enable is not set to true.
```
## 发送消息
``` shell
kafka-console-producer.sh --broker-list CentOS1:9092 --topic config
```
## 消费消息
``` shell
kafka-console-consumer.sh --bootstrap-server CentOS2:9092 --topic config
```
如果需要重头消费
``` shell
kafka-console-consumer.sh --bootstrap-server CentOS2:9092 --topic config --from-beginning
```
## topic详情
``` java
[root@CentOS2 config]# kafka-topics.sh --zookeeper CentOS2:2181 --describe --topic hostInstall
Topic: hostInstall	PartitionCount: 3	ReplicationFactor: 3	Configs:
	Topic: hostInstall	Partition: 0	Leader: 1	Replicas: 1,3,0	Isr: 1,3,0
	Topic: hostInstall	Partition: 1	Leader: 3	Replicas: 3,0,1	Isr: 3,0,1
	Topic: hostInstall	Partition: 2	Leader: 0	Replicas: 0,1,3	Isr: 0,1,3
```
## 分区修改
``` java
[root@CentOS2 config]# kafka-topics.sh --zookeeper CentOS1:2181 --alter --topic config --partitions 6
WARNING: If partitions are increased for a topic that has a key, the partition logic or ordering of the messages will be affected
Adding partitions succeeded!
```