## 1. 环境准备

### 1.1 JDK

- 官网：https://www.oracle.com/java/technologies/javase-downloads.html

  > windows版本： [jdk-8u271-windows-x64.exe](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html#license-lightbox)
  >
  > linux版本： [jdk-8u271-linux-x64.tar.gz](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html#license-lightbox)

- 配置环境变量

  > linux

  ```
  [root@centos-7 jdk1.8.0_271]# vim /etc/profile
  export JAVA_HOME=/usr/local/jdk1.8.0_271
  export PATH=$JAVA_HOME/bin:$PATH
  
  [root@centos-7 jdk1.8.0_271]# source /etc/profile
  ```

  > windows

  ```
  #添加系统环境变量
  JAVA_HOME	->	D:\Java\jdk1.8.0_271
  CLASSPATH	->	.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
  PATH		->	%JAVA_HOME%\bin
  				%JAVA_HOME%\jre\bin
  ```

- 服务命令

  | 说明     | 命令          |
  | -------- | ------------- |
  | 查看版本 | java -version |

### 1.2 防火墙命令

- 防火墙

  | 说明           | 命令                        |
  | -------------- | --------------------------- |
  | 启动           | systemctl start firewalld   |
  | 停止           | systemctl stop firewalld    |
  | 查看状态       | systemctl status firewalld  |
  | 开机启动防火墙 | systemctl enable firewalld  |
  | 开机禁用防火墙 | systemctl disable firewalld |

- 端口

  | 说明 | 命令                                                        |
  | ---- | ----------------------------------------------------------- |
  | 新增 | firewall-cmd --zone=public --add-port=80/tcp --permanent    |
  | 删除 | firewall-cmd --zone=public --remove-port=80/tcp --permanent |
  | 重载 | firewall-cmd --reload                                       |
  | 查看 | firewall-cmd --list-ports                                   |

## 2. zookeeper

### 2.1 依赖

- JDK 

### 2.2 下载

- 官网：https://zookeeper.apache.org/releases.html
- 3.6.2：https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz

### 2.3 部署

- 文件位置：	/root/software/zookeeper

- 版本：3.6.2

- 解压

  ```
  tar -zxf apache-zookeeper-3.6.2-bin.tar.gz -C /root/software/zookeeper
  ```

- 设置环境变量

  > [root@master apache-zookeeper-3.6.2-bin]# vim /etc/profile

  ```
  #ZK_HOME
  export ZOOKEEPER_HOME=/root/software/zookeeper/apache-zookeeper-3.6.2-bin
  export PATH=$PATH:$ZOOKEEPER_HOME/bin
  ```

  > [root@master apache-zookeeper-3.6.2-bin]# source /etc/profile

#### 2.3.1 单机

- 配置文件

  > [root@master conf]# cd /root/software/zookeeper/apache-zookeeper-3.6.2-bin/conf
  >
  > [root@master conf]# cp zoo_sample.cfg zoo.cfg
  >
  > [root@master conf]# vim zoo.cfg

  ```
  # 修改数据文件位置
  dataDir=/root/logs/zookeeper
  ```

#### 2.3.2 集群

- 参考：https://www.cnblogs.com/ysocean/p/9860529.html

- 配置文件

  ***将原先 zoo_sample.cfg 配置文件 复制一份并改名为  zoo.cfg***

  > [root@master conf]# cd /root/software/zookeeper/apache-zookeeper-3.6.2-bin/conf
  >
  > [root@master conf]# cp zoo_sample.cfg zoo.cfg
  >
  > [root@master conf]# vim zoo.cfg

  ```
  # 修改数据文件位置
  dataDir=/root/software/zookeeper/apache-zookeeper-3.6.2-bin/data
  
  #最后一行新增(当前节点server的ip设置为0.0.0.0， 如master节点)
  server.1=0.0.0.0:2888:3888
  server.2=slave1:2888:3888
  server.3=slave2:2888:3888
  ```

  ***每个节点基于 zoo.cfg 中 dataDir 的文件位置 新增 myid文件， 且将server的值输入myid***（如master节点的server.1）

  > [root@master conf]# mkdir -p /root/software/zookeeper/apache-zookeeper-3.6.2-bin/data
  >
  > [root@master conf]# echo 1 > /root/software/zookeeper/apache-zookeeper-3.6.2-bin/data/myid

- 端口开放

  | 端口 | 说明                                   |
  | ---- | -------------------------------------- |
  | 2181 | zookeeper客户端默认端口                |
  | 2888 | 集群内机器通讯使用（Leader监听此端口） |
  | 3888 | 选举leader使用                         |

- 说明

  > 1. zoo.cfg配置文件中server的port需要对外开放，否则不同节点端口不同，服务起不来
  >
  > 2. 加载 zoo.cfg 时，判断 server.id 数量，当超过一个时获取 dataDir目录下的myid文件的值，确定server的 ip和端口

- 参考

  > 安装集群：https://www.cnblogs.com/ysocean/p/9860529.html
  >
  > 集群启动流程过程说明：https://www.cnblogs.com/rexcheny/articles/9381360.html
  >
  > 集群启动流程源码分析：https://blog.csdn.net/qq_43792385/article/details/105104015

### 2.4 服务命令

| 说明     | 命令                    |
| -------- | ----------------------- |
| 启动     | bin/zkServer.sh start   |
| 查看状态 | bin/zkServer.sh status  |
| 停止     | bin/zkServer.sh stop    |
| 重启     | bin/zkServer.sh restart |

### 2.5 zk客户端命令

-  连接

  > [root@master apache-zookeeper-3.6.2-bin]# bin/zkCli.sh -server 127.0.0.1:2181

- 操作说明

  | 说明                     | 命令              |
  | ------------------------ | ----------------- |
  | 显示根目录下、文件       | ls /              |
  | 创建文件，并设置初始内容 | create /zk "test" |
  | 获取文件内容             | get /zk           |
  | 修改文件内容             | set /zk "zkbak"   |
  | 删除文件                 | delete /zk        |
  | 退出客户端               | quit              |
  | 帮助命令                 | help              |

  