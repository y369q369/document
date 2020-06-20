<h1><center>confluent platform使用</center></h1>

### 1. 安装

#### 1.1 local安装

- 官网：https://docs.confluent.io/current/quickstart/cos-quickstart.html

- 教程：https://blog.csdn.net/u012551524/article/details/103686739

- 实操

  1. 配置jdk（1.8↑）

     ```
     配置环境变量
     export JAVA_HOME=/repository/java/jdk1.8.0_221
     export PATH=$JAVA_HOME/bin:$PATH
     
     查看是否安装成功
     [root@gs /]# java -version
     java version "1.8.0_221"
     Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
     Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
     ```

  2.  安装confluent platform

     ```
     1. 下载软件
     地址：https://www.confluent.io/download/?_ga=2.258962954.1696288973.1592612668-453778093.1576842144
     
     2. 解压
     tar -zxf confluent-5.5.0-2.12.tar.gz -C /repository/confluent
     
     3. 配置环境变量
     export CONFLUENT_HOME=/repository/confluent/confluent-5.5.0
     export PATH=$PATH:$CONFLUENT_HOME/bin
     
     4. 安装Confluent Cli
     curl -L https://cnfl.io/cli | sh -s -- -b /repository/confluent/confluent-5.5.0/bin
     
     5. 安装Kafka Connect Datagen 
     $CONFLUENT_HOME/bin/confluent-hub install --no-prompt confluentinc/kafka-connect-datagen:latest
     
     6. 启动
     confluent local start
     
     7. 停止
     confluent local stop
     
     ```

  

