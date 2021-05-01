# RabbitMQ



## 一. 单机部署

### 1.1 yum的repo部署

> 查看 repo 库

```bash
1. yum repolist
2. ls /etc/yum.repos.d
```

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

>  5、设置开机启动 

```bash
systemctl enable rabbitmq-server.service #设置开机吭劢

systemctl start rabbitmq-server #启动服务 

rabbitmq 配置文件位置：
ls /var/lib/rabbitmq/

ls /etc/rabbitmq/enabled_plugins
```

>  6、启动rabbitmq服务 

```bash
service rabbitmq-server start
```

>  7、启用管理后台 

```bash
rabbitmq-plugins enable rabbitmq_management
```

>  8、添加用户 

```bash
rabbitmqctl add_user test 123456
```

>  9、配置用户权限 

```bash
rabbitmqctl set_user_tags test administrator
```

>  10、通过浏览器访问管理后台 

```bash
RabbitMQ管理后台：http://<您的 CVM IP 地址>:15672/
用户名：test 密码：123456
```

>  11、服务关闭： 

```bash
service rabbitmq-server stop
```

>  运行 RabbitMQ 监听端口号：

```bash
4369 #erlang 収现口
5672 #client 端通信口
15672 #管理界面 ui 端口
25672 #server 间内部通信口 
```

