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

```bash
# 1.解压  				
tar -zxf kafka_2.13-2.4.1.tgz -C /opt/

# 2.切换kafka目录		   
cd /opt/kafka_2.13-2.4.1

# 3.启动自带zookeeper	   
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties

# 4.启动kafka			
bin/kafka-server-start.sh -daemon config/server.properties

# 查看进程
[root@gs ~]# jps
3201 QuorumPeerMain
5992 Jps
5916 Kafka
```

#### 2.2 相关脚本命令

```bash
# 创建topic: test
bin/kafka-topics.sh --zookeeper localhost:2181 --create --replication-factor 1 --partitions 1 --topic test

# 查看topic列表
bin/kafka-topics.sh --zookeeper localhost:2181 --list

# 查看指定topic的详细信息
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic test

# 查看所有topic的配置信息
bin/kafka-configs.sh --zookeeper localhost:2181 --entity-type topics --describe
# 查看指定topic配置信息
bin/kafka-configs --zookeeper localhost:2181 --entity-type topics --entity-name test --describe

# topic修改配置(两种方式)
# 一、
bin/kafka-configs.sh --zookeeper localhost:2181 --entity-type topics --entity-name test --alter --add-config retention.ms=9223372036854775807,cleanup.policy=delete
# 二、
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic test --config retention.ms=86400000

# topic删除配置（两种方式）
# 一、
bin/kafka-configs.sh --zookeeper localhost:2181 --entity-type topics --entity-name test --alter --delete-config retention.ms
# 二、
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic test --delete-config retention.ms

# 删除topic
bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic test

# 查看消费组
bin/kafka-consumer-groups.sh --command-config /home/yangq/consumer.properties --bootstrap-server localhost:9092 --list

# 查看某一消费组
bin/kafka-consumer-groups.sh --command-config /home/yangq/consumer.properties --bootstrap-server localhost:9092 --group KMOffsetCache-p-tdhsit-mg2 --describe


```

> command-config配置文件（kafka的安全认证信息）

```bash
# Consumer properties
sasl.mechanism=GSSAPI
security.protocol=SASL_PLAINTEXT
sasl.kerberos.service.name=kafka
```

#### 2.3 生产消费数据

```bash
# 生产数据
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

# 消费数据
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092  --from-beginning --topic test （从头消费）
```

#### 2.4 配置

> server.properties

```bash
# 监听端口，默认为主机名（设置为主机ip或0.0.0.0）
listeners=PLAINTEXT://0.0.0.0:9092

# 提供给生产者和消费者的ip,没有配置使用listeners的值（不能为0.0.0.0,只能是真实ip）
advertised.listeners=PLAINTEXT://192.168.72.161:9092

# 彻底删除topic
delete.topic.enable=true

# 允许自动创建topic：生产消费时无topic会自动创建topic
auto.create.topics.enable=false
```

