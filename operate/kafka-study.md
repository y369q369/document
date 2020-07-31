<h1><center>kafka-study</center></h1>

### 1. 下载

```
地址：			http://kafka.apache.org/downloads.html
```

- window

  ```
  Binary downloads  ->  Scala 2.13  - kafka_2.13-2.4.1.tgz (asc, sha512)  ->  http镜像
  ```

- linux

  ```
  wget -P software/ http://mirror.bit.edu.cn/apache/kafka/2.4.1/kafka_2.13-2.4.1.tgz
  ```

### 2. 单机安装

#### 2.1 启动

```
1.解压  				tar -zxf kafka_2.13-2.4.1.tgz -C /opt/
2.切换kafka目录		   cd /opt/kafka_2.13-2.4.1
3.启动自带zookeeper	   bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
4.启动kafka			bin/kafka-server-start.sh -daemon config/server.properties

查看进程
[root@gs ~]# jps
3201 QuorumPeerMain
5992 Jps
5916 Kafka
```

#### 2.2 相关脚本命令

```
创建topic: test
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

查看topic列表
bin/kafka-topics.sh --list --zookeeper localhost:2181

查看指定topic的详细信息
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic test

删除topic
bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic test
```

#### 2.3 生产消费数据

```
生产数据
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

消费数据
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test
```

#### 2.4 配置

```
server.properties
delete.topic.enable=true
auto.create.topics.enable=false
```

