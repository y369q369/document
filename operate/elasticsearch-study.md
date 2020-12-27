 <h1><center>elasticsearch学习过程</center></h1>
### 1. 安装

### 1.1 centos7.5

- 参考： https://www.cnblogs.com/zhenghengbin/p/12250234.html

- 环境需求

  > elasticsearch7 之后，默认需求jdk11
  >
  > 为保证安全性，不能用root启动

  ```
  # 创建用户es
  useradd es
  
  # 修改密码
  passwd es
  
  # 切换es登录
  su es
  ```

- 获取安装包

  > 官网： https://www.elastic.co/cn/downloads/elasticsearch
  >
  > 下载：https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-linux-x86_64.tar.gz

- 修改配置文件

  >  核心配置文件：conf/elasticsearch.yml

  ```
  node.name: node-1
  
  path.data: /home/es/software/elasticsearch-7.10.0/data
  
  path.logs: /var/log/elasticearch
  
  network.host: 0.0.0.0
  
  cluster.initial_master_nodes: ["node-1"]
  
  ```

  > jvm参数：config/jvm.options

  ```
  # Xms represents the initial size of total heap space
  # Xmx represents the maximum size of total heap space
  
  -Xms128m
  -Xmx128m
  ```

- 启动

  ```
  # 前台启动
  bin/elasticsearch
  
  # 后台启动
  bin/elasticsearch -d
  ```

- 错误处理

  ```
  ERROR: [3] bootstrap checks failed
  [1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
  [2]: max number of threads [3795] for user [esuser] is too low, increase to at least [4096]
  [3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
  ```

  > 修改  /etc/security/limits.conf 

  ```
  # 最下方增加下面内容
  * soft nofile 65536
  * hard nofile 131072
  * soft nproc 2048
  * hard nproc 4096
  ```

  >  /etc/sysctl.conf 

  ```
  # 最下方增加
  vm.max_map_count=262145
  
  # 刷新sysctl
  sysctl -p
  
  # 重新登陆es用户操作
  su - es
  ```

- 访问

  ```
  ip:9200
  ```


### 3 api

官网： https://www.elastic.co/guide/en/elasticsearch/reference/7.10/cat.html

获取所有index:  /_cat/indices