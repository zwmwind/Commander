# Kafka相关
## 安装Kafka

```
docker pull wurstmeister/kafka 
docker pull zookeeper
```
-  windows安装
  直接下载zip包

## 启动Kafka

- 运行zookeeper

  ```
  docker run -d --name dzookeeper -p 2181:2181 -t zookeeper
  ```
- 运行Kafka（单机）

  ```
  docker run -d --name dkafka \
  -p 9092:9092 \
  -e KAFKA_BROKER_ID=0 \
  -e KAFKA_ZOOKEEPER_CONNECT=【你的ip】:2181 \
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://【你的ip】:9092 \
  -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka
  ```
  注意【你的ip】指的是容器的宿主机的ip
- 运行kafka(非docker方式)
  ```
  nohup ./zookeeper-server-start.sh ../config/zookeeper.properties &
  nohup ./kafka-server-start.sh ../config/server.properties &
  ```
- windows运行
  ```
  bin\windows\zookeeper-server-start.bat confg\zookeeper.properties
  bin\windows\kafka-server-satrt.bat config\server.properties
  ```

## 相关配置

- 打开相关防火墙

  ```
  firewall-cmd --zone=public --add-port=2181/tcp --permanent
  firewall-cmd --zone=public --add-port=9092/tcp --permanent
  ```
## 使用Kafka
- 注意
  windows下使用命令为:
  ```
  bin\windows\xxx.bat 
  ```
  早期版本
  ```
  --bootstrap-server localhost:9092
  ```
  可以替换为
  ```
  --zookeeper localhost:2181
  
- 进入Kafka

  ```
  docker exec -it kafka /bin/bash
  ```
  
## Topic相关
- 创建topic
  ```
  kafka-topics.sh --create --bootstrap-server localhost:9092  replication-factor 1 --partitions 1 --topic test
  ```
- 查看topic
  ```
  kafka-topics.sh --list --bootstrap-server localhost:9092
  kafka-topics.sh --describe --bootstrap-server localhost:9092
  kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic test
  ```
- 改变分区数量(只能增加)
  ```
   kafka-topics.sh --bootstrap-server localhost:9092 --alter --partitions 4 --topic test
  ```
- 删除分区
  ```
  kafka-topics.sh --bootstrap-server locahost:9092 --delete --topic test
## 生产者相关
- 启动生产者
  ```
  kafka-console-producer.sh --broker-list localhost:9092 --tpoic test
  ```
## 消费者相关
- 启动消费者(消费某个topic中最老的数据)
  ```
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-begining
- 启动消费者并指定groupId(配置文件中指定)
  ```
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --consumer.config ../config/consumer.properties
  ```
- 启动消费者并将配置文件中的groupId对应的offset删除(即初始化该groupId的offset)
  ```
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --consumer.config ../config/consumer.properties --delete-consumber-offsets --from-begining
  ```
- 查看消费者group
  ```
  kafka-consumer-groups.sh --bootstrap-server loaclhost:9092 --list
  ```
- 查看某个消费组的消费情况(消息队列堆积情况)
  ```
  kafka-consumer-groups.sh --bootstrap-server loaclhost:9092 --group test-consumer-group --describe
  ```
  - 注意
    没有改消费者组会报错[not esxist]
## zookeepershell
- 进入zookeeper shell
  ```
  zookeeper-shell.sh localhost:2181
  ```
- 查看Kafka节点数量
  ```
  ls /brokers/ids
  ```
## 偏移量相关
- 通过zookeeper客户端对topic某一分区的offset进行修改(zookeeper shell)
  ```
  get /consermers/test-consumer-group/offset/test/0
  get /consermers/test-consumer-group/offset/test/0 1000
  ```
- 通过kakfa内置的kafka.tools修改所有分区为最旧或最新
  ```
  kafka-run-class.sh kafka.tools.UpdateOffsetInZK earlies ../config/consumer.properties test
  kafka-run-class.sh kafka.tools.UpdateOffsetInZK latest ../config/consumer.properties test
  ```
- 0.11.0.0及以上版本通过kafka.consumer-groups.sh脚本
  ```
  kafka-consumer-groups.sh --bootstrap localhost:9092 --group test-consumer-group --topic test:0 --reset-offsets --to-earliest -execute
  kafka-consumer-groups.sh --bootstrap localhost:9092 --group test-consumer-group --topic test:0 --reset-offsets --to-latest -execute
  kafka-consumer-groups.sh --bootstrap localhost:9092 --group test-consumer-group --topic test:0 --reset-offsets --to-offset 1000 -execute
  ```