# RabbitMQ



## 一. 单机部署

### 1.1 非容器

> 查看 repo 库

```
1. yum repolist
2. ls /etc/yum.repos.d
```

#### 1.1.1 rpm包安装

> 1、安装erlang环境

```bash
# 创建erlang.repo库 
curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash

# 下载erlang安装包
yumdownloader --resolve --destdir=./erlang erlang

# 安装erlang
rpm -ivh erlang-23.3.2-1.el8.x86_64.rpm
```

> 2、安装socat环境

```bash
# 下载socat安装包
yumdownloader --resolve --destdir=./socat socat

# 安装erlang
rpm -ivh socat-1.7.3.3-2.el8.x86_64.rpm
```

> 3、安装RabbitMQ

```bash
# 创建erlang.repo库 
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash

# 下载RabbitMQ-Server安装包
yumdownloader --resolve --destdir=./rabbitmq-server rabbitmq-server

# 安装RabbitMQ-Server
rpm -ivh rabbitmq-server-3.8.16-1.el8.noarch.rpm
```

#### 1.1.2 yum的repo安装

> 1、创建erlang.repo库 

```bash
curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash
```

> 2、安装erlang 

```bash
yum install erlang
```

> 3、创建rabbitmq-server.repo库 

```bash
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
```

> 4、安装rabbitmq-server 

```bash
yum install rabbitmq-server
```

#### 1.1.3 启动

>  1、环境配置

```bash
# 开机启动
systemctl enable rabbitmq-server.service

# rabbitmq 配置文件位置
ls /var/lib/rabbitmq/
# rabbitmq 管理插件文件位置
ls /etc/rabbitmq/enabled_plugins
```

>  2、启动rabbitmq服务 

```bash
service rabbitmq-server start
```

>  3、启用管理后台 

```bash
rabbitmq-plugins enable rabbitmq_management
```

>  4、添加用户 

```bash
rabbitmqctl add_user test test
```

>  5、配置用户权限 

```bash
rabbitmqctl set_user_tags test administrator
```

>  6、通过浏览器访问管理后台 

```bash
RabbitMQ管理后台：http://<您的 CVM IP 地址>:15672/
用户名：test 密码：test
```

>  7、服务关闭： 

```bash
service rabbitmq-server stop
```

>  8、运行 RabbitMQ 监听端口号：

```bash
4369    # erlang 収现口
5672    # client 端通信口
15672   # 管理界面 ui 端口
25672   # server 间内部通信口 
```

