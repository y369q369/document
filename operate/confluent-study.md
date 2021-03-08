<h1><center>confluent platform使用</center></h1>
### 1. 安装

#### 1.1 local安装

- 官网：https://docs.confluent.io/current/quickstart/cos-quickstart.html

- 教程：https://blog.csdn.net/u012551524/article/details/103686739

- 实操

  1. 配置jdk（1.8↑）

     ```
     配置环境变量 (当前终端使用直接执行， 永久生效修改 /etc/profile )
     export JAVA_HOME=/repository/java/jdk1.8.0_221
     export PATH=$JAVA_HOME/bin:$PATH
     
     查看是否安装成功
     [root@gs /]# java -version
     java version "1.8.0_221"
     Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
     Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
     ```

  2. 安装confluent platform

     - 下载解压
  
     ```
     官网地址：  https://www.confluent.io/download/?_ga=2.258962954.1696288973.1592612668-453778093.1576842144
     直链地址：  https://packages.confluent.io/archive/6.1/confluent-6.1.0.tar.gz
               https://packages.confluent.io/archive/6.0/confluent-6.0.0.tar.gz
               https://packages.confluent.io/archive/5.5/confluent-5.5.1-2.12.tar.gz
               https://packages.confluent.io/archive/5.4/confluent-5.4.2-2.12.tar.gz
               https://packages.confluent.io/archive/5.3/confluent-5.3.4-2.12.tar.gz
        https://packages.confluent.io/archive/6.0/confluent-community-6.0.0.tar.gz
        https://packages.confluent.io/archive/5.5/confluent-community-5.5.1-2.12.tar.gz
        https://packages.confluent.io/archive/5.4/confluent-community-5.4.2-2.12.tar.gz
        https://packages.confluent.io/archive/5.3/confluent-community-5.3.4-2.12.tar.gz
     
     解压：     tar -zxf confluent-5.5.1-2.12.tar.gz -C /repository/confluent
  ```
     
  - 配置环境变量 (可不配，直接取目录下执行)
     
     ```
     export CONFLUENT_HOME=/repository/confluent/confluent-5.5.1
     export PATH=$PATH:$CONFLUENT_HOME/bin
  ```
     
- confluent-hub (正式版自带)
     
     ```
     作用： 管理connector的工具，可在线下载已有的connector
     官网： https://docs.confluent.io/current/connect/managing/confluent-hub/client.html#hub-linux
     下载： http://client.hub.confluent.io/confluent-hub-client-latest.tar.gz?_ga=2.225202706.983708684.1601083707-453778093.1576842144
     安装： 将解压后的 bin/ etc/ share/java/ 目录下文件放到 $CONFLUENT_HOME 对应目录下
  ```
     
  - Confluent Cli (正式版 5.3 以上可不用)
     
     ```
     作用：获取confluent的启动脚本
     方式一(乱码)：
       官网教程： https://docs.confluent.io/5.3.4/cli/installing.html#cli-install
       使用 Tarball installation 获取， 解压tar即可， 
       将加压后的 confluent/confluent 复制到对应 $CONFLUENT_HOME}/bin
       
     方式二(源码编译)：
       地址： https://github.com/confluentinc/confluent-cli
       按照 README.md 操作即可
       注意： 生成的 $CONFLUENT_HOME}/bin/confluent 执行时 如若报错/usr/bin/env: “bash\r”: 没有那个文件或目录， 需要将编码格式更换， 参考
             https://blog.csdn.net/qq_32642107/article/details/90677061
             https://blog.csdn.net/xiongchun11/article/details/78832947
  ```
     
  - Kafka Connect Datagen（基于confluent-hub）
     
     ```
     作用： kafka-connector的模拟数据demo
     安装： $CONFLUENT_HOME/bin/confluent-hub install --no-prompt confluentinc/kafka-connect-datagen:latest
     
     [root@gs bin]# ./confluent-hub install --no-prompt confluentinc/kafka-connect-datagen:latest
     Running in a "--no-prompt" mode 
     Implicit acceptance of the license below:  
     Apache License 2.0 
     https://www.apache.org/licenses/LICENSE-2.0 
     Downloading component Kafka Connect Datagen 0.4.0, provided by Confluent, Inc. from Confluent Hub and installing into /repository/confluent/confluent-5.3.4/share/confluent-hub-components 
     Adding installation directory to plugin path in the following files: 
       /repository/confluent/confluent-5.3.4/etc/kafka/connect-distributed.properties 
       /repository/confluent/confluent-5.3.4/etc/kafka/connect-standalone.properties 
       /repository/confluent/confluent-5.3.4/etc/schema-registry/connect-avro-distributed.properties 
       /repository/confluent/confluent-5.3.4/etc/schema-registry/connect-avro-standalone.properties 
      
     Completed 
  ```
     
  - 相关命令
     
     ```
     ./confluent
     confluent: A command line interface to manage Confluent services
     
     Usage: confluent <command> [<subcommand>] [<parameters>]
     
     These are the available commands:
     
         acl         Specify acl for a service.
         config      Configure a connector.
         consume     Consume data from topics
         current     Get the path of the data and logs of the services managed by the current confluent run.
                     Override default setting with "CONFLUENT_CURRENT" environment variable.
         demo        Run demos provided in GitHub repo https://github.com/confluentinc/examples
         destroy     Delete the data and logs of the current confluent run.
         list        List available services.
         load        Load a connector.
         log         Read or tail the log of a service.
         produce     Produce data to topics
         start       Start all services or a specific service along with its dependencies
         status      Get the status of all services or the status of a specific service along with its dependencies.
         stop        Stop all services or a specific service along with the services depending on it.
         top         Track resource usage of a service.
         unload      Unload a connector.
         version     Print the Confluent Platform flavor and version or the individual version of a service.
     
     'confluent help' lists available commands. See 'confluent help <command>' to read about a
     specific command.
  ```
     
     
  
  

