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

  > 80为示例端口

  | 说明 | 命令                                                        |
  | ---- | ----------------------------------------------------------- |
  | 新增 | firewall-cmd --zone=public --add-port=80/tcp --permanent    |
  | 删除 | firewall-cmd --zone=public --remove-port=80/tcp --permanent |
  | 重载 | firewall-cmd --reload                                       |
  | 查看 | firewall-cmd --list-ports                                   |

### 1.3 用户命令

- 用户组

  > hadoop为示例用户组

  | 说明 | 命令                             |
  | ---- | -------------------------------- |
  | 查看 | awk -F : '{print $1}' /etc/group |
  | 新建 | groupadd hadoop                  |
  | 删除 | groupdel hadoop                  |

- 用户

  > hadoop为示例用户

  | 说明                 | 命令                              |
  | -------------------- | --------------------------------- |
  | 查看                 | awk -F : '{print $1}' /etc/passwd |
  | 新增用户及同名用户组 | useradd hadoop                    |
  | 改密                 | passwd hadoop                     |
  | 删除                 | userdel -rf hadoop                |
  | 查看用户详情         | id hadoop                         |
  | 用户添加到组         | usermod -G #{用户组} #{用户}      |

### 1.4 SSH 免密登录

- 示例：master节点免密登录slave1节点（如果需要双向，重复多次操作）

  > master节点操作

  ```
  # 生成rsa凭证（遇到提示信息，一直按回车就可以）
  [root@master zookeeper]# ssh-keygen -t rsa
  Generating public/private rsa key pair.
  Enter file in which to save the key (/root/.ssh/id_rsa): 
  Enter passphrase (empty for no passphrase): 
  Enter same passphrase again: 
  Your identification has been saved in /root/.ssh/id_rsa.
  Your public key has been saved in /root/.ssh/id_rsa.pub.
  The key fingerprint is:
  SHA256:1XX5Tkpx0KrNOpxQ39vy5FS/UVu/OOz+rv7ppxdGRq0 root@master
  The key's randomart image is:
  +---[RSA 2048]----+
  |              oo+|
  |           . ..++|
  |          . . .=.|
  |         .  . Eoo|
  |        S  . *o=+|
  |          . . =oX|
  |           o.o.oO|
  |            =oo=O|
  |            +BO&+|
  +----[SHA256]-----+
  
  # 复制 id_rsa.pub 公匙 到slave1节点
  [root@master .ssh]# scp /root/.ssh/id_rsa.pub root@slave1:/root
  ```

  > slave1节点操作

  ```
  # 生成rsa凭证（遇到提示信息，一直按回车就可以）
  [root@slave1 .ssh]# ssh-keygen -t rsa
  Generating public/private rsa key pair.
  Enter file in which to save the key (/root/.ssh/id_rsa): 
  Enter passphrase (empty for no passphrase): 
  Enter same passphrase again: 
  Your identification has been saved in /root/.ssh/id_rsa.
  Your public key has been saved in /root/.ssh/id_rsa.pub.
  The key fingerprint is:
  SHA256:DpuJREW12723J2InWO+Wvq7qk8vmYupczzvyoB1bTHE root@slave1
  The key's randomart image is:
  +---[RSA 2048]----+
  |     .o..        |
  |     .   .       |
  |    .   . . E    |
  |   .     o +     |
  |    . . S o .    |
  |   . . * o  ..   |
  |    . + = o+.... |
  |     . ++O* +.*..|
  |     .=ooB@B.X*+ |
  +----[SHA256]-----+
  
  # 复制master的公匙id_rsa.pub到 authorized_keys 文件中
  [root@slave1 .ssh]# cat /root/id_rsa.pub >> /root/.ssh/authorized_keys
  
  # 修改 authorized_keys 权限为600（非root用户不修改不能免密登录）
  [root@slave1 .ssh]# chmod 600 /root/.ssh/authorized_keys
  ```

## 2. zookeeper

### 2.1 依赖

- JDK 

### 2.2 下载

- 官网：https://zookeeper.apache.org/releases.html

  > 选择[Apache ZooKeeper 3.6.2](https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz)([asc](https://downloads.apache.org/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz.asc), [sha512](https://downloads.apache.org/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz.sha512))下载， [Apache ZooKeeper 3.6.2 Source Release](https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2.tar.gz)([asc](https://downloads.apache.org/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2.tar.gz.asc), [sha512](https://downloads.apache.org/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2.tar.gz.sha512))下载后需要自行编译

- 3.6.2：https://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz

### 2.3 部署

- 文件位置：	/opt/zookeeper

- 版本：3.6.2

- 解压

  ```
  tar -zxf apache-zookeeper-3.6.2-bin.tar.gz -C /opt/zookeeper
  ```

- 设置环境变量

  > [root@master apache-zookeeper-3.6.2-bin]# vim /etc/profile

  ```
  #ZK_HOME
  export ZOOKEEPER_HOME=/opt/zookeeper/apache-zookeeper-3.6.2-bin
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
  dataDir=/opt/zookeeper/apache-zookeeper-3.6.2-bin/data
  ```

#### 2.3.2 集群

- 参考：https://www.cnblogs.com/ysocean/p/9860529.html

- 节点：至少3个，且为奇数

- 配置文件

  ***将原先 zoo_sample.cfg 配置文件 复制一份并改名为  zoo.cfg***

  > [root@master conf]# cd /opt/zookeeper/apache-zookeeper-3.6.2-bin/conf
  >
  > [root@master conf]# cp zoo_sample.cfg zoo.cfg
  >
  > [root@master conf]# vim zoo.cfg

  ```
  # 修改数据文件位置
  dataDir=/opt/zookeeper/apache-zookeeper-3.6.2-bin/data
  
  #最后一行新增(当前节点server的ip设置为0.0.0.0， 如master节点)
  server.1=0.0.0.0:2888:3888
  server.2=slave1:2888:3888
  server.3=slave2:2888:3888
  ```

  ***每个节点基于 zoo.cfg 中 dataDir 的文件位置 新增 myid文件， 且将server的值输入myid***（如master节点的server.1）

  > [root@master conf]# mkdir -p /opt/zookeeper/apache-zookeeper-3.6.2-bin/data
  >
  > [root@master conf]# echo 1 > /opt/zookeeper/apache-zookeeper-3.6.2-bin/data/myid

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

## 3. hadoop

### 3.1 依赖

- JDK

### 3.2 下载

- 官网：https://hadoop.apache.org/releases.html

  > 说明：选择**Binary download**的镜像源下载，解压可直接使用

- 3.3.0：https://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz

### 3.3 部署

#### 3.3.0 前置工作

- 文件位置：	/opt/hadoop

- 版本：3.3.0

- 解压

  ```
  tar -zxf hadoop-3.3.0.tar.gz -C /opt/hadoop
  ```

- 设置环境变量

  > [root@master hadoop-3.3.0]# vim /etc/profile

  ```
  #HADOOP_HOME
  export HADOOP_HOME=/opt/hadoop/hadoop-3.3.0
  export PATH=$PATH:$HADOOP_HOME/bin
  export PATH=$PATH:$HADOOP_HOME/sbin
  ```

  > [root@master hadoop-3.3.0]# source /etc/profile

- 创建hadoop用户及hadoop用户组

  ```
  # 创建用户hadoop及用户组hadoop，并将用户hadoop用户放在hadoop用户组中
  useradd hadoop
  
  # 改密用户hadoop
  passwd hadoop
  
  #查看用户情况
  id hadoop
  ```

  > 赋予hadoop用户管理员(sudo)权限

  ```
  vim /etc/sudoers
  
  #新增一行
  hadoop  ALL=(ALL)       NOPASSWD:ALL
  ```

- 节点免密登录

  > hadoop用户登录本节点

  ```
  # 查看用户家目录下有没有.ssh目录(有./ssh目录则表示生成过rsa凭证)
  [hadoop@master ~]$ ll -a
  total 22
  drwx------. 5 hadoop hadoop  158 Dec 28 01:23 .
  drwxr-xr-x. 5 root   root     43 Dec 27 19:46 ..
  -rw-------. 1 hadoop hadoop 1566 Dec 28 01:27 .bash_history
  -rw-r--r--. 1 hadoop hadoop   18 Apr 10  2018 .bash_logout
  -rw-r--r--. 1 hadoop hadoop  193 Apr 10  2018 .bash_profile
  -rw-r--r--. 1 hadoop hadoop  231 Apr 10  2018 .bashrc
  drwxrwxr-x. 3 hadoop hadoop   18 Dec 27 20:33 .cache
  drwxrwxr-x. 3 hadoop hadoop   18 Dec 27 20:33 .config
  -rw-------. 1 hadoop hadoop  863 Dec 28 01:23 .viminfo
  
  # 生成rsa凭证
  [hadoop@master ~]$ ssh-keygen -t rsa
  
  # 复制.ssh目录下的 id_rsa.pub 内容 到 authorized_keys 中
  [hadoop@master ~]$ cat .ssh/id_rsa.pub >> .ssh/authorized_keys
  
  # 修改authorized_keys权限
  [hadoop@master ~]$ chmod 600 .ssh/authorized_keys
  ```

  > master节点hadoop用户 免密登录 slave1节点hadoop用户 

  ```
  # 复制master节点 id_rsa.pub 到 slave1节点
  [hadoop@master ~]$ scp .ssh/authorized_keys hadoop@master:/home/hadoop
  
  # 复制.ssh目录下的 id_rsa.pub 内容 到 authorized_keys 中
  [hadoop@slave1 ~]$ cat .ssh/id_rsa.pub >> .ssh/authorized_keys
  
  # 修改authorized_keys权限
  [hadoop@slave1 ~]$ chmod 600 .ssh/authorized_keys
  ```

  > 操作建议： 可以先将所有节点的公匙放到 master节点的.ssh/authorized_keys中，直接复制master节点的 .ssh/authorized_keys 给其他节点

- 对外访问端口

| service      | name                                 | 2.x port | 3.x port | desc                                                         |
| ------------ | ------------------------------------ | -------- | -------- | ------------------------------------------------------------ |
| hdfs         | fs.default.name                      | 9000     | 9000     | hdfs默认端口                                                 |
| Namenode     | dfs.namenode.https-address           | 50470    | 9871     | The namenode secure http server address and port.            |
|              | dfs.namenode.http-address            | 50070    | 9870     | The address and the base port where the dfs namenode web ui will listen on. |
|              | fs.defaultFS                         | 8020     | 9820     | 指定HDFS运行时nameNode地址                                   |
| Secondary NN | dfs.namenode.secondary.https-address | 50091    | 9869     | The secondary namenode HTTPS server address and port         |
|              | dfs.namenode.secondary.http-address  | 50090    | 9868     | The secondary namenode HTTPS server address and port         |
| Datanode     | dfs.datanode.ipc.address             | 50020    | 9867     | The datanode ipc server address and port.                    |
|              | dfs.datanode.address                 | 50010    | 9866     | The datanode server address and port for data transfer.      |
|              | dfs.datanode.https.address           | 50475    | 9865     | The datanode secure http server address and port             |
|              | dfs.datanode.http.address            | 50075    | 9864     | The datanode http server address and por                     |
| Yarn         | yarn.resourcemanager.webapp.address  | 8088     | 8088     | http服务端口                                                 |

> 操作

```
[root@slave2 ~]# firewall-cmd --list-ports

[root@slave2 ~]# firewall-cmd --zone=public --add-port=9871/tcp --add-port=9870/tcp--add-port=9820/tcp --add-port=9869/tcp --add-port=9868/tcp --add-port=9867/tcp --add-port=9866/tcp --add-port=9865/tcp --add-port=9864/tcp --add-port=8088/tcp --add-port=9000/tcp --permanent
success

[root@slave2 ~]# firewall-cmd --reload
success

[root@slave2 ~]# firewall-cmd --list-ports
3888/tcp 2888/tcp 2181/tcp 9871/tcp 9870/tcp 9869/tcp 9868/tcp 9867/tcp 9866/tcp 9865/tcp 9864/tcp 8088/tcp 9000/tcp
```

#### 3.3.1 伪分布式

##### 参考 

https://www.cnblogs.com/thousfeet/p/8618696.html

##### 配置文件

```
# 切换到配置文件目录
cd /opt/hadoop/hadoop-3.3.0/etc/hadoop/
```

> **hadoop-env.sh**

```
# 修改JAVA_HOME
export JAVA_HOME=/usr/java/jdk1.8.0_221
```

> **core-site.xml**

```
# fs.defaultFS ： 分布式集群中主节点的地址 : 指定端口号
# hadoop.tmp.dir： 指定hadoop进程运行中产生的数据存放的工作目录，NameNode、DataNode等就在本地工作目录下建子目录存放数据。但事实上在生产系统里，NameNode、DataNode等进程都应单独配置目录，而且配置的应该是磁盘挂载点，以方便挂载更多的磁盘扩展容量

<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/opt/hadoop/hadoop-3.3.0/data</value>
        </property>
</configuration>
```

> **hdfs-site.xml**

```
# dfs.replication： 配置hdfs的副本数。value一般指定3，但因为搭建的是伪分布式就只有一台机器，所以只能写1

<configuration>
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
</configuration>
```

>  **mapred-site.xml** 

```
# mapreduce.framework.name： 指定MapReduce程序应该放在哪个资源调度集群上运行。若不指定为yarn，那么MapReduce程序就只会在本地运行而非在整个集群中运行。

<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
```

> **yarn-site.xml**

```
# yarn.resourcemanager.hostname： 指定yarn集群中的老大
# yarn.nodemanager.aux-services： 配置yarn集群中的重节点，指定map产生的中间结果传递给reduce采用的机制是shuffle

<configuration>

<!-- Site specific YARN configuration properties -->

        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>master</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>

</configuration>
```

##### 启动

```
# 第一次安装hadoop，需要格式化
[hadoop@master hadoop-3.3.0]$ bin/hdfs namenode -format

# 启动hdfs
[hadoop@master hadoop]$ start-dfs.sh 
Starting namenodes on [master]
Starting datanodes
Starting secondary namenodes [master]

# 查看进程
[hadoop@master hadoop]$ jps
2034 SecondaryNameNode
1817 DataNode
1707 NameNode
2159 Jps

# 停止hdfs
[hadoop@master hadoop]$ stop-dfs.sh 
Stopping namenodes on [master]
Stopping datanodes
Stopping secondary namenodes [master]
```

#### 3.3.2 全分布式

##### 参考

https://blog.csdn.net/VanasWang/article/details/105451198

##### 节点分配

|      | master                             | slave1                                       | slave2         |
| ---- | ---------------------------------- | -------------------------------------------- | -------------- |
| HDFS | NameNode（NN）<br />DataNode（DN） | Secondary NameNode（SN）<br />DataNode（DN） | DataNode（DN） |
| YARN | ResourceManager <br />NodeManager  | NodeManager                                  | NodeManager    |

##### 配置文件

```
# 切换到配置文件目录
cd /opt/hadoop/hadoop-3.3.0/etc/hadoop/
```

> **hadoop-env.sh**

```
# 修改JAVA_HOME
export JAVA_HOME=/usr/java/jdk1.8.0_221
```

> **core-site.xml**

```
# fs.defaultFS ： 分布式集群中namenode节点的地址 : 指定端口号
# hadoop.tmp.dir： 指定hadoop进程运行中产生的数据存放的工作目录，NameNode、DataNode等就在本地工作目录下建子目录存放数据。但事实上在生产系统里，NameNode、DataNode等进程都应单独配置目录，而且配置的应该是磁盘挂载点，以方便挂载更多的磁盘扩展容量

<configuration>
        <!-- 指定NameNode的地址 -->
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
        </property>
        
        <!-- 指定hadoop的数据存储地址 -->
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/opt/hadoop/hadoop-3.3.0/data</value>
        </property>
</configuration>
```

> **hdfs-site.xml**

```
<configuration>
		<!-- datanode副本数，值 <= datanode节点数量 -->
		<!-- <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property> -->

        <!-- namenode数据的存储目录 -->
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>file://${hadoop.tmp.dir}/namenode</value>
        </property>
 
        <!-- Secondary namenode数据的存储目录 -->
        <property>
                <name>dfs.namenode.checkpoint.dir</name>
                <value>file://${hadoop.tmp.dir}/secondaryNamenode</value>
        </property>

        <!-- datanode数据的存储目录 -->
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>file://${hadoop.tmp.dir}/datanode</value>
        </property>
        
        <!-- namenode节点web访问地址 -->
        <property>
                <name>dfs.namenode.http-address</name>
                <value>master:9870</value>
        </property>

        <!-- secondary namenode节点的web访问地址 -->
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>slave1:9868</value>
        </property>
</configuration>
```

> workers （配置后可start-dfs.sh 一键启动）

```
# 配DataNode节点
master
slave1
slave2
```

> mapred-site.xml

```
# mapreduce.framework.name： 指定MapReduce程序应该放在哪个资源调度集群上运行。若不指定为yarn，那么MapReduce程序就只会在本地运行而非在整个集群中运行。

<configuration>
        <!-- 指定MR运行在YARN上 -->
		<property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
```

>  yarn-site.xml

```
# yarn.resourcemanager.hostname： 指定yarn集群中的主节点
# yarn.nodemanager.aux-services： 配置yarn集群中的重节点，指定map产生的中间结果传递给reduce采用的机制是shuffle

<configuration>

<!-- Site specific YARN configuration properties -->

        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>master</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>

</configuration>
```

##### 节点同步

复制hadoop文件夹到另外两个节点

```
# 主节点 到 slave1
scp -r /opt/hadoop/ slave1:/opt

# 主节点 到 slave2
scp -r /opt/hadoop/ slave2:/opt
```

##### 启动

> 准备工作

```
# 第一次安装hadoop，需要格式化
[hadoop@master hadoop-3.3.0]$ bin/hdfs namenode -format

# 删除历史数据及日志
[hadoop@master hadoop-3.3.0]$ rm -rf logs data

# 创建数据目录
[hadoop@master hadoop-3.3.0]$ mkdir -p data/namenode
[hadoop@master hadoop-3.3.0]$ mkdir -p data/secondaryNamenode
[hadoop@master hadoop-3.3.0]$ mkdir -p data/datanode
```

> 单节点启停

```
# master节点启动 namenode 和 datanode
[hadoop@master hadoop-3.3.0]$ hdfs --daemon start namenode
WARNING: /opt/hadoop/hadoop-3.3.0/logs does not exist. Creating.
[hadoop@master hadoop-3.3.0]$ hdfs --daemon start datanode
[hadoop@master hadoop-3.3.0]$ jps
3808 DataNode
3868 Jps
3695 NameNode

# slave1 启动 Secondary NameNode 和 datanode
[hadoop@slave1 hadoop-3.3.0]$ hdfs --daemon start secondarynamenode
[hadoop@slave1 hadoop-3.3.0]$ hdfs --daemon start datanode
[hadoop@slave1 hadoop-3.3.0]$ jps
2450 SecondaryNameNode
2518 DataNode
2566 Jps

# slave2 启动 datanode
[hadoop@slave2 hadoop-3.3.0]$ hdfs --daemon start datanode
WARNING: /opt/hadoop/hadoop-3.3.0/logs does not exist. Creating.
[hadoop@slave2 hadoop-3.3.0]$ jps
2154 DataNode
2202 Jps

# 停止
[hadoop@master hadoop-3.3.0]$ hdfs --daemon stop namenode
[hadoop@master hadoop-3.3.0]$ hdfs --daemon stop datanode
[hadoop@slave1 hadoop-3.3.0]$ hdfs --daemon stop secondarynamenode
[hadoop@slave1 hadoop-3.3.0]$ hdfs --daemon stop datanode
[hadoop@slave2 hadoop-3.3.0]$ hdfs --daemon stop datanode
```

> 集群操作

```
# 启动hdfs
[hadoop@master hadoop]$ start-dfs.sh 
Starting namenodes on [master]
Starting datanodes
Starting secondary namenodes [slave1]

# 停止hdfs
[hadoop@master ~]$ stop-dfs.sh 
Stopping namenodes on [master]
Stopping datanodes
Stopping secondary namenodes [slave1]

# 启动yarn
[hadoop@master hadoop]$ start-yarn.sh
Starting resourcemanager
Starting nodemanagers

# 停止yarn
[hadoop@master hadoop]$ stop-yarn.sh
Stopping nodemanagers
slave1: WARNING: nodemanager did not stop gracefully after 5 seconds: Trying to kill with kill -9
slave2: WARNING: nodemanager did not stop gracefully after 5 seconds: Trying to kill with kill -9
Stopping resourcemanager
```

#### 3.3.3 HA

##### 参考

https://blog.csdn.net/l1682686/article/details/107997323

##### 节点分配

|                 | master | slave1 | slave2 |
| --------------- | ------ | ------ | ------ |
| NameNode        | yes    | yes    |        |
| DataNode        | yes    | yes    | yes    |
| JournalNode     | yes    | yes    | yes    |
| NodeManager     | yes    | yes    | yes    |
| ResourceManager |        | yes    | yes    |
| Zookeeper       | yes    | yes    | yes    |
| ZKFC            | yes    | yes    | yes    |

##### 配置文件

```
# 切换到配置文件目录
cd /opt/hadoop/hadoop-3.3.0/etc/hadoop/
```

> **hadoop-env.sh**

```
# 修改JAVA_HOME
export JAVA_HOME=/usr/java/jdk1.8.0_221
```

> **core-site.xml**

```
<configuration>

        <!-- 指定NameNode的地址 -->
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://mycluster</value>
        </property>

        <!-- 指定hadoop的数据存储地址 -->
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/opt/hadoop/hadoop-3.3.0/ha-data</value>
        </property>

        <!-- 指定zookeeper地址 -->
        <property>
                <name>ha.zookeeper.quorum</name>
                <value>master:2181,slave1:2181,slave2:2181</value>
        </property>

        <!-- 指定hadoop链接zookeeper超时时长 -->
        <property>
                <name>ha.zookeeper.session-timeout.ms</name>
                <value>3000</value>
        </property>

</configuration>
```

>  **hdfs-site.xml** 

```
<configuration>
        <!-- 指定副本数 -->
        <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>

        <!-- 指定hdfs的nameservice为cluster1，需要和core-site.xml中的保持一致 -->
        <property>
                <name>dfs.nameservices</name>
                <value>mycluster</value>
        </property>

        <!-- cluster下面有2个NameNode，分别是nn1，nn2 -->
        <property>
                <name>dfs.ha.namenodes.mycluster</name>
                <value>nn1,nn2</value>
        </property>

        <!-- nn1的RPC通信地址 -->
        <property>
                <name>dfs.namenode.rpc-address.mycluster.nn1</name>
                <value>master:8020</value>
        </property>

        <!-- nn2的RPC通信地址 -->
        <property>
                <name>dfs.namenode.rpc-address.mycluster.nn2</name>
                <value>slave1:8020</value>
        </property>

        <!-- nn1的http通信地址 -->
        <property>
                <name>dfs.namenode.http-address.mycluster.nn1</name>
                <value>master:9870</value>
        </property>

        <!-- nn2的http通信地址 -->
        <property>
                <name>dfs.namenode.http-address.mycluster.nn2</name>
                <value>slave1:9870</value>
        </property>

        <!-- 指定NameNode的edits元数据的共享存储位置。也就是JournalNode列表
                该url的配置格式：qjournal://host1:port1;host2:port2;host3:port3/journalId
                journalId推荐使用nameservice，默认端口号是：8485 -->
        <property>
                <name>dfs.namenode.shared.edits.dir</name>
                <value>qjournal://master:8485;slave1:8485;slave2:8485/mycluster</value>
        </property>
        <!-- 配置失败自动切换实现类 -->
        <property>
                <name>dfs.client.failover.proxy.provider.mycluster</name>
                <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
        </property>

        <!-- 配置隔离机制方法，多个机制用换行分隔，即每个机制暂用一行 -->
        <property>
                <name>dfs.ha.fencing.methods</name>
                <value>sshfence</value>
        </property>

        <!-- 使用sshfence隔离机制时需要ssh免登陆 -->
        <property>
                <name>dfs.ha.fencing.ssh.private-key-files</name>
                <value>/home/hadoop/.ssh/id_rsa</value>
        </property>

        <!-- 配置sshfence隔离机制超时时间 -->
        <property>
                <name>dfs.ha.fencing.ssh.connect-timeout</name>
                <value>30000</value>
        </property>

        <!-- 开启NameNode失败自动切换 -->
        <property>
                <name>dfs.ha.automatic-failover.enabled</name>
                <value>true</value>
        </property>


</configuration>
```

>  **mapred-site.xml** 

```
<configuration>

        <!-- 指定mr框架为yarn方式  -->
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>

        <!-- 指定mapreduce jobhistory地址  -->
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>master:10020</value>
        </property>

        <!-- 任意历史服务器的web地址  -->
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>master:19888</value>
        </property>

</configuration>

```

> **yarn-site.xml**

```
<configuration>

<!-- Site specific YARN configuration properties -->


        <!-- 开启RM高可用 -->
        <property>
                <name>yarn.resourcemanager.ha.enabled</name>
                <value>true</value>
        </property>

        <!-- 指定RM的cluster id -->
        <property>
                <name>yarn.resourcemanager.cluster-id</name>
                <value>yrc</value>
        </property>

        <!-- 指定RM名字 -->
        <property>
                <name>yarn.resourcemanager.ha.rm-ids</name>
                <value>rm1,rm2</value>
        </property>

        <!-- 指定rm1地址 -->
        <property>
                <name>yarn.resourcemanager.hostname.rm1</name>
                <value>slave1</value>
        </property>

        <!-- 指定rm2地址 -->
        <property>
                <name>yarn.resourcemanager.hostname.rm2</name>
                <value>slave2</value>
        </property>

        <!-- 指定zk集群地址 -->
        <property>
                <name>yarn.resourcemanager.zk-address</name>
                <value>master:2181,slave1:2181,slave2:2181</value>
        </property>

        <!-- 配置yarn集群中的重节点，指定map产生的中间结果传递给reduce采用的机制是shuffle -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>

        <!-- 是否启用日志聚合功能，日志聚合开启后保存到HDFS上 -->
        <property>
                <name>yarn.log-aggregation-enable</name>
                <value>true</value>
        </property>

        <!-- 聚合后的日志在HDFS上保存多长时间，单位为s -->
        <property>
                <name>yarn.log-aggregation.retain-seconds</name>
                <value>86400</value>
        </property>

        <!-- 启用自动恢复 -->
        <property>
                <name>yarn.resourcemanager.recovery.enabled</name>
                <value>true</value>
        </property>

        <!-- 指定resourcemanager的状态信息存储到zookeeper集群上 -->
        <property>
                <name>yarn.resourcemanager.store.class</name>
                <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
        </property>

</configuration>
```

##### 节点配置文件同步

```
# 主节点 到 slave1
[hadoop@master etc]$ scp -r hadoop/ slave1:/opt/hadoop/hadoop-3.3.0/etc

# 主节点 到 slave2
[hadoop@master etc]$ scp -r hadoop/ slave2:/opt/hadoop/hadoop-3.3.0/etc
```

##### 开放端口

```
# 涉及端口
3888/tcp 2888/tcp 2181/tcp 9871/tcp 9870/tcp 9869/tcp 9868/tcp 9867/tcp 9866/tcp 9865/tcp 9864/tcp 8088/tcp 9000/tcp 8031/tcp 10020/tcp 19888/tcp

# 开放端口
[root@slave2 ~]# firewall-cmd --zone=public --add-port=9871/tcp --add-port=9870/tcp--add-port=9820/tcp --add-port=9869/tcp --add-port=9868/tcp --add-port=9867/tcp --add-port=9866/tcp --add-port=9865/tcp --add-port=9864/tcp --add-port=8088/tcp --add-port=9000/tcp --add-port=8031/tcp --add-port=10020/tcp --add-port=19888/tcp --permanent
success
```

##### 启动

> 启动zk集群（root用户）

```
# 启动（3个节点）
[root@master ~]# zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /opt/zookeeper/apache-zookeeper-3.6.2-bin/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

# 启动过几秒钟后查看各节点状态（3个节点）
[root@master ~]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /opt/zookeeper/apache-zookeeper-3.6.2-bin/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower

# 查看进程
[root@slave2 ~]# jps
3399 Jps
2992 QuorumPeerMain
```

> 启动 journalnode

```
# 启动（3个节点）
[hadoop@master etc]$ hdfs --daemon start journalnode

# 查看进程
[hadoop@master etc]$ jps
3399 Jps
3390 JournalNode
```

> 启动 namenode

```
# 格式化 namenode:  ( master操作, 可以先删除ha/journalnode/目录下的文件)
[hadoop@master etc]$ hdfs namenode -format

# 启动master的namenode
[hadoop@master etc]$ hdfs --daemon start namenode

# slave1同步，先保证有一个namenode启动
[hadoop@slave1 hadoop]$ hdfs namenode -bootstrapStandby

# 查看namenode状态
[hadoop@master etc]$ hdfs haadmin -getServiceState nn1
active
[hadoop@master etc]$ hdfs haadmin -getServiceState nn2
standby
```

> 启动 hdfs

```
# 格式化zkfc ( master )
[hadoop@master etc]$  hdfs zkfc -formatZK

# 一键启动hdfs ( master )
[hadoop@master etc]$ start-dfs.sh

# master节点进程
[hadoop@master etc]$ jps
3541 NameNode
3910 DataNode
4280 DFSZKFailoverController
4328 Jps
3390 JournalNode

# slave1节点进程
[hadoop@slave1 hadoop]$ jps
3426 JournalNode
3671 DataNode
3597 NameNode
3887 DFSZKFailoverController
3919 Jps

# slave2节点进程
[hadoop@slave2 ~]$ jps
3400 DataNode
3180 JournalNode
3517 Jps
```

> 启动 yarn

```
# 一键启动yarn ( master )
[hadoop@master etc]$ start-yarn.sh
Starting resourcemanagers on [ slave1 slave2]
Starting nodemanagers

# master节点进程
[hadoop@master etc]$ jps
4834 Jps
3541 NameNode
3910 DataNode
4280 DFSZKFailoverController
4712 NodeManager
3390 JournalNode
```

>  启动 mapreduce任务历史服务器 

```
# 启动 （master）
[hadoop@master etc]$ mapred --daemon start historyserver

# master节点进程
[hadoop@master etc]$ jps
3541 NameNode
3910 DataNode
4280 DFSZKFailoverController
4712 NodeManager
4938 Jps
3390 JournalNode
4894 JobHistoryServer
```

### 3.4 访问

- 地址：http://192.168.72.133:9870

### 3.5 服务命令

| 说明             | 命令                        |
| ---------------- | --------------------------- |
| 初始化（格式化） | bin/hadoop namenode -format |
| 启动hdfs         | sbin/start-dfs.sh           |
| 停止hdfs         | sbin/stop-dfs.sh            |
| 启动yarn         | sbin/start-yarn.sh          |
| 停止yarn         | sbin/stop-yarn.sh           |

### 3.6 hdfs命令行操作

> 命令地址： /opt/hadoop/hadoop-3.3.0/bin/hadoop

| 说明                         | 命令                                      |
| ---------------------------- | ----------------------------------------- |
| 查询目录                     | hadoop fs -ls /                           |
| 创建目录                     | hadoop fs -mkdir /test                    |
| 上传本地文件到hdfs           | hadoop fs -put ./test.txt /test           |
|                              | hadoop fs -copyFromLocal ./test.txt /test |
| hdfs拉文件到本地             | hadoop fs -get /test/test.txt             |
|                              | hadoop fs -getToLocal /test/test.txt      |
| 复制文件                     | hadoop fs -cp /test/test.txt /test1       |
| 删除文件                     | hadoop fs -rm /test1/test.txt             |
| 移动文件                     | hadoop fs -mv /test/test.txt /test1       |
| 删除文件夹                   | hadoop fs -rm -r /test1                   |
| 查阅帮助，该命令为查看ls命令 | hadoop fs -help ls                        |
| 查看文件内容                 | hadoop fs -cat README.txt                 |

### 3.7 说明

####  jps进程说明

| 进程名称                | 程序                                                         |
| ----------------------- | ------------------------------------------------------------ |
| QuorumPeerMain          | zk启动的进程                                                 |
| ZooKeeperMain           | zkCli客户端启动的进程（可以通过quit关闭客户端，close关闭连接） |
| JournalNode             | journalnode启动进程                                          |
| DFSZKFailoverController | zkfc的故障转移                                               |
| JobHistoryServer        | yarn日志聚合                                                 |

#### 坑点

- 建议 hadoop 用户使用服务命令启动，当使用root用户操作时，报错如下

  ```
  [root@master sbin]# ./start-dfs.sh
  Starting namenodes on [master]
  ERROR: Attempting to operate on hdfs namenode as root
  ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
  Starting datanodes
  ERROR: Attempting to operate on hdfs datanode as root
  ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
  Starting secondary namenodes [slave1]
  ERROR: Attempting to operate on hdfs secondarynamenode as root
  ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
  ```

  解决方案参考： https://blog.csdn.net/lglglgl/article/details/80553828

- window上用api时访问hadoop时，报错

  ```
  java.io.FileNotFoundException: java.io.FileNotFoundException: HADOOP_HOME and hadoop.home.dir are unset. -see https://wiki.apache.org/hadoop/WindowsProblems
  ```

  解决：系统环境变量中新增 HADOOP_HOME 即可

  ```
  could not locate Hadoop executable: G:\software\hadoop\hadoop-3.3.0\bin\winutils.exe
  ```

  解决：下载winutils.exe相关文件放到hadoop/bin目录下，下载地址：https://gitee.com/jiashu_wu/winutils

## 4. hbase

### 4.1 依赖

- JDK

### 4.2 下载

- 官网：http://hbase.apache.org/downloads.html

  > 说明：选择 **bin** 的镜像源下载，解压可直接使用

- 2.4.1：https://mirror.bit.edu.cn/apache/hbase/2.4.1/hbase-2.4.1-bin.tar.gz

- 2.3.4：https://mirror.bit.edu.cn/apache/hbase/2.3.4/hbase-2.3.4-bin.tar.gz

### 4.3 部署

#### 4.3.0 前置工作

- 文件位置：	/opt/hbase

- 版本：2.3.4

- 解压

  ```
  tar -zxf hbase-2.3.4-bin.tar.gz -C /opt/hbase
  ```

- 设置环境变量

  > [root@master apache-zookeeper-3.6.2-bin]# vim /etc/profile

  ```
  #HBASE_HOME
  export HBASE_HOME=/opt/hbase/hbase-2.3.4
  export PATH=$PATH:$HBASE_HOME/bin
  ```

  > [root@master apache-zookeeper-3.6.2-bin]# source /etc/profile

- 对外访问端口

  | service       | name                         | port  | port             |
| ------------- | ---------------------------- | ----- | ---------------- |
  | HMaster       | hbase.master.port            | 16000 | RegionServer接入 |
| HMaster       | hbase.master.info.port       | 16010 | 集群监控         |
  | HRegionServer | hbase.regionserver.port      | 16020 | 客户端接入       |
  | HRegionServer | hbase.regionserver.info.port | 16030 | 节点监控         |
  
  9000
  
  > 操作
  
  ```
  [root@slave2 ~]# firewall-cmd --zone=public --add-port=16000/tcp --add-port=16010/tcp--add-port=16020/tcp --add-port=16030/tcp --permanent
  success
  
  [root@slave2 ~]# firewall-cmd --reload
success
  
  [root@slave2 ~]# firewall-cmd --list-ports
  ```
  
  

#### 4.3.1 单机部署

##### 参考 

https://blog.csdn.net/zhangjunli/article/details/111035332

##### 配置文件

```
# 切换到配置文件目录
cd opt/hbase/hbase-2.3.4/conf
```

- **使用自带zk**

> **hbase-env.sh**

```
# 修改JAVA_HOME
export JAVA_HOME=/usr/java/jdk1.8.0_221

# 禁止使用hadoop的jar包
export HBASE_DISABLE_HADOOP_CLASSPATH_LOOKUP="true"
```

> **hbase-site.xml**

```
<configuration>
        <property>
                <name>hbase.cluster.distributed</name>
                <value>false</value>
        </property>
        <property>
                <name>hbase.tmp.dir</name>
                <value>/opt/hbase/hbase-2.3.4/tmp</value>
         </property>
         <property>
                 <name>hbase.unsafe.stream.capability.enforce</name>
                 <value>false</value>
         </property>
         <property>
                <name>hbase.rootdir</name>
                <value>/opt/hbase/hbase-2.3.4/data</value>
        </property>
</configuration>
```

- 使用外部zk

> **hbase-env.sh**

```
# 修改JAVA_HOME
export JAVA_HOME=/usr/java/jdk1.8.0_221

# 是否启用内部zk管理
export HBASE_MANAGES_ZK=false

# 禁止使用hadoop的jar包
export HBASE_DISABLE_HADOOP_CLASSPATH_LOOKUP="true"
```

> **hbase-site.xml**

```
<configuration>
        <property>
                <name>hbase.cluster.distributed</name>
                <value>true</value>
        </property>
        <property>
                <name>hbase.tmp.dir</name>
                <value>/opt/hbase/hbase-2.3.4/tmp</value>
         </property>
         <property>
                 <name>hbase.unsafe.stream.capability.enforce</name>
                 <value>false</value>
         </property>
         <property>
                <name>hbase.rootdir</name>
                <value>/opt/hbase/hbase-2.3.4/data</value>
        </property>
</configuration>
```

##### 启停

```
# 启动
[root@centos-73 conf]# start-hbase.sh 
running master, logging to /opt/hbase/hbase-2.3.4/logs/hbase-root-master-centos-73.out

# 停止
[root@centos-73 conf]# stop-hbase.sh 
stopping hbase.cd.^H...........

# 使用shell
[root@centos-73 ~]# hbase shell
2021-02-07 21:47:10,856 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.4.1, rb4d9639f66fccdb45fea0244202ffbd755341260, Fri Jan 15 10:58:57 PST 2021
Took 0.0016 seconds 
```



#### 4.4 访问

- 地址： http://120.26.184.85:16010/master-status

#### 4.5 shell命令

> 命令地址： /opt/hbase/hbase-2.3.4/bin/hbase

| 命令        | 说明                                                         | 示例                                                         |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| version     | 返回hbase版本信息                                            | version                                                      |
| status      | 返回hbase集群的状态信息                                      | status                                                       |
| table_help  | 查看如何操作表                                               | table_help                                                   |
| create      | 创建表                                                       | create ‘表名’, ‘列族名1’, ‘列族名2’... ‘列族名N’             |
| alter       | 修改列族                                                     | 添加一个列族：alter ‘表名’, ‘列族名’ 删除列族：alter ‘表名’, {NAME=> ‘列族名’, METHOD=> ‘delete’} |
| describe    | 显示表相关的详细信息                                         | describe/desc ‘表名’                                         |
| list        | 列出hbase中存在的所有表                                      | list                                                         |
| exists      | 测试表是否存在                                               | exists ‘表名’                                                |
| put         | 添加或修改的表的值                                           | put ‘表名’, ‘行键’, ‘列族名’, ‘列值’ put ‘表名’, ‘行键’, ‘列族名:列名’, ‘列值’ |
| scan        | 通过对表的扫描来获取对用的值                                 | scan ‘表名’ <br/>扫描某个列族： scan ‘表名’, {COLUMN=>‘列族名’} <br/>扫描某个列族的某个列： scan ‘表名’, {COLUMN=>‘列族名:列名’} <br/>查询同一个列族的多个列： scan ‘表名’, {COLUMNS => [ ‘列族名1:列名1’, ‘列族名1:列名2’, …]} |
| get         | 获取行或单元（cell）的值                                     | get ‘表名’, ‘行键’ get ‘表名’, ‘行键’, ‘列族名’              |
| count       | 统计表中行的数量                                             | count ‘表名’                                                 |
| incr        | 增加指定表行或列的值                                         | incr ‘表名’, ‘行键’, ‘列族:列名’, 步长值                     |
| get_counter | 获取计数器                                                   | get_counter ‘表名’, ‘行键’, ‘列族:列名’                      |
| delete      | 删除指定对象的值（可以为表，行，列对应的值，另外也可以指定时间戳的值） | 删除列族的某个列： delete ‘表名’, ‘行键’, ‘列族名:列名’      |
| deleteall   | 删除指定行的所有元素值                                       | deleteall ‘表名’, ‘行键’                                     |
| truncate    | 重新创建指定表                                               | truncate ‘表名’                                              |
| enable      | 使表有效                                                     | enable ‘表名’                                                |
| is_enabled  | 是否启用                                                     | is_enabled ‘表名’                                            |
| disable     | 使表无效                                                     | disable ‘表名’                                               |
| is_disabled | 是否无效                                                     | is_disabled ‘表名’                                           |
| drop        | 删除表（drop的表必须是disable的）                            | disable ‘表名’ drop ‘表名’                                   |
| shutdown    | 关闭hbase集群（与exit不同）                                  |                                                              |
| tools       | 列出hbase所支持的工具                                        |                                                              |
| exit        | 退出hbase shell                                              |                                                              |

#### 4.6 说明

##### hadoop版本匹配

官网文档： http://hbase.apache.org/book.html

搜索关键词： *Hadoop version support matrix* 