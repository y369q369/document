## 1. 环境准备

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

  