# elasticsearch-study

### 1. 安装

#### 1.1 centos8

- 环境准备

  > jdk11

  >  es不能使用root用户启动，由于ElasticSearch可以接收用户输入的脚本并且执行，为了系统安全考虑，建议创建一个单独的用户用来运行ElasticSearch

  ```
  # 创建用户 esuser
  useradd esuser
  # 修改用户密码
  passwd esuser
  # 切换用户
  su esuser
  ```

- 安装

  > 下载地址： https://www.elastic.co/cn/downloads/elasticsearch

  ```
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-linux-x86_64.tar.gz
  tar -zxf elasticsearch-7.10.0-linux-x86_64.tar.gz
  ```

  