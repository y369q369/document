<h1><center>mysql-study</center></h1>
### 1.安装 (centos7)

#### 1.1 单机 

参考：  https://segmentfault.com/a/1190000019507071 

##### rpm安装方式

- 检查 MariaDB

  ```
  [root@centos7 ~]# rpm -qa | grep mariadb
  mariadb-libs-5.5.56-2.el7.x86_64
  ```

- 删除 MariaDB (安装mysql可删除mariadb, 安装mysql后会覆盖原来的mariadb)

  ```
  [root@centos7 ~]# rpm -e --nodeps mariadb-libs
  ```

- 检查mysql

  ```
  [root@centos7 ~]# rpm -qa | grep mysql
  ```

- 删除mysql 

  ```
  1. 删除所有rpm包 (5.7 和 8.0安装包一样， 修改版本即可)
  [root@grassprince ~]# rpm -e --nodeps mysql-community-libs-8.0.21-1.el7.x86_64
  [root@grassprince ~]# rpm -e --nodeps mysql-community-common-8.0.21-1.el7.x86_64
  [root@grassprince ~]# rpm -e --nodeps mysql-community-client-8.0.21-1.el7.x86_64
  [root@grassprince ~]# rpm -e --nodeps mysql-community-server-8.0.21-1.el7.x86_64
  
  2. 删除所有mysql文件
  find / -name mysql
  rm -rf /var/lib/mysql
  
  3. 删除mysql配置文件
  rm -rf /etc/my.cnf
  rm -rf /root/.mysql_sercret
  ```

- 下载 mysql

   ```
地址：       https://dev.mysql.com/downloads/mysql/
  选择：       Red Hat Enterprise Linux / Oracle Linux
               Red Hat Enterprise Linux 7 / Oracle Linux 7 (x86, 64-bit)
  下载：       页面  bundle (下方第一个)
              命令行 wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.21-1.el7.x86_64.rpm-bundle.tar   (8.1)
              wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.31-1.el7.x86_64.rpm-bundle.tar  (5.7)
  ```
  
  
  
- 装 mysql

   ```
   1. 解压
      tar -xvf mysql-8.0.21-1.el7.x86_64.rpm-bundle.tar -C ./mysql-community-8
      cd mysql-community-8
   
   2. 安装 (5.7 和 8.0安装包一样， 修改版本即可)
      rpm -ivh mysql-community-common-8.0.21-1.el7.x86_64.rpm
      rpm -ivh mysql-community-libs-8.0.21-1.el7.x86_64.rpm
      rpm -ivh mysql-community-client-8.0.21-1.el7.x86_64.rpm
      rpm -ivh mysql-community-server-8.0.21-1.el7.x86_64.rpm
   ```
   
- 启动 mysql

     ```
     启动：     systemctl start mysqld.service
     查看状态：  systemctl status mysqld.service
     停止：     systemctl stop mysqld.service
     重启：     systemctl restart mysqld.service
     
     查看版本：  mysql -V
     ```
     
-    设置

  ```
  1. 查看原始密码
  MySQL第一次启动后会创建超级管理员账号root@localhost，初始密码存储在日志文件中
  [root@centos7 software]# grep 'temporary password' /var/log/mysqld.log
  2020-08-17T08:03:26.511233Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: UDkeGyJA.72O
  
  2. 登录 (获取第一步显示的密码)
  mysql -uroot -pUDkeGyJA.72O
  
  3. 修改密码规则 (修改规则前需要修改密码才能操作)
  ALTER USER 'root'@'localhost' IDENTIFIED BY 'Yq1988.ssa';
  SHOW VARIABLES LIKE 'validate_password%';
  set global validate_password.length=6;
  set global validate_password.policy=0;
  
  4. 修改默认密码
  ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
  
  5. 修改 root 用户 host
  use mysql;
  update user set host='%' where user='root';
  
  6. 允许root远程登录(退出数据库后重新进入赋权限)
  GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
  FLUSH PRIVILEGES;
  
  7. 开启远程访问端口
  查看已开放的端口     firewall-cmd --list-ports
  开启端口            firewall-cmd --zone=public --add-port=3600/tcp --permanent
  关闭端口            firewall-cmd --zone= public --remove-port=3600/tcp --permanent
  重启防火墙          firewall-cmd --reload
  ```

- 开机启动

  ```
  systemctl enable mysqld
  systemctl daemon-reload
  ```

#### 1.2 集群

##### 主从复制

- 参考          https://blog.csdn.net/java_cxrs/article/details/97043997 

- 节点分配

  | ip             | 用途      | mysql版本 |
  | -------------- | --------- | --------- |
  | 192.168.72.152 | Master/写 | 8.0.21    |
  | 192.168.72.153 | Slave/读  | 8.0.21    |
  | 192.168.72.154 | Slave/读  | 8.0.21    |

- 按照单节点先把三台机器都装好mysql并运行

- 主节点配置

  ```
  1. 修改配置文件 
  vim /etc/my.cnf
    文件最后添加
       log-bin=mysql-bin
       server-id=1
       
  2.  在Master库创建一个账号，具备Replication Slave权限，提供给Slave库访问二进制日志使用
  登录mysql:   mysql -uroot -p123456
  创建账号:     mysql> CREATE USER 'sluser'@'192.168.72.%' IDENTIFIED WITH mysql_native_password BY '123456';
  账号赋权:     mysql> GRANT REPLICATION SLAVE ON *.* TO 'sluser'@'192.168.72.%';
  刷新权限:     mysql> flush privileges;
  
  3. 重启 (重启后密码安全等级可能需要重新设置)
  systemctl restart mysqld
  
  4. 查看主节点的状态
  mysql> show master status;
  +------------------+----------+--------------+------------------+-------------------+
  | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
  +------------------+----------+--------------+------------------+-------------------+
  | mysql-bin.000001 |      875 |              |                  |                   |
  +------------------+----------+--------------+------------------+-------------------+
  
  5. 查看主节点日志大小
  mysql> show master logs;
  +------------------+-----------+-----------+
  | Log_name         | File_size | Encrypted |
  +------------------+-----------+-----------+
  | mysql-bin.000001 |       156 | No        |
  +------------------+-----------+-----------+
  ```

- 从节点配置

  ```
  1. 修改配置文件 (153节点的server-id=2, 154节点的server-id=3)
  vim /etc/my.cnf
    文件最后添加
       server-id=2
       
  2. 重启 (重启后密码安全等级可能需要重新设置)
  systemctl restart mysqld
  
  3. 登录mysql
  mysql -uroot -p123456
  
  4. 设置主节点参数
  mysql> CHANGE MASTER TO  MASTER_HOST='192.168.72.152', MASTER_USER='sluser', MASTER_PASSWORD='123456', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=875;
  
  5. 启动从服务器复制功能
  mysql> start slave;
  
  6. 查看从服务器的状态
  mysql> show slave status \G;
    出现如下配置说明配置成功
      Slave_IO_Running:Yes
      Slave_SQL_Running: Yes
  ```

  - 备注

    1.  主节点参数说明

       | 参数            | 说明                         |
       | --------------- | ---------------------------- |
       | MASTER_HOST     | master的ip或虚ip或域名       |
       | MASTER_USER     | master上之前创建的复制账户名 |
       | MASTER_PASSWORD | 创建的复制账户密码           |
       | MASTER_LOG_FILE | master的 File                |
       | MASTER_LOG_POS  | master的 Position            |

    2. 修改主节点参数

       ```
       重置主节点日志文件数据(清空)
       mysql> reset master;
       
       停止从节点的复制， 并重置从节点
       mysql> stop slave;reset slave;
       
       修改主节点ip
       mysql> change master to master_host='192.168.72.152';
       
       修改主节点ip
       mysql> change master to master_log_pos=344;
       
       查看从服务器的状态
       mysql> show slave status \G;
       ```

-  测试主从复制功能

  ```
  主节点 创建 数据库
  mysql> create database `test`;
  
  从节点 查看数据库 (能看到test数据库则，集群部署成功)
  mysql> show databases;
  ```

  

##### 常见错误

```
错误信息
Host '192.168.72.153' is blocked because of many connection errors; unblock with 'mysqladmin flush-hosts', Error_code: MY-001129

原因
同一个ip在短时间内产生太多（超过mysql数据库max_connection_errors的最大值）中断的数据库连接而导致的阻塞

解决
[root@centos7 ~]# whereis mysqladmin
mysqladmin: /usr/bin/mysqladmin /usr/share/man/man1/mysqladmin.1.gz
[root@centos7 ~]# /usr/bin/mysqladmin flush-hosts -h192.168.72.153 -uroot -p

mysql> flush hosts;
Query OK, 0 rows affected (0.00 sec)

cat /dev/null > /var/log/mysqld.log
```



### 2. 操作

#### 2.1 命令行

##### 登录

```
登录本机
mysql -uroot -p
mysql -u root -p
mysql -uroot -p123456
mysql -u root -p 123456

登录远程主机
mysql -h [host] -u [username] -p 
mysql -h 192.168.72.152 -u sluser -p
```

##### 密码

```
查看当前密码的规则
SHOW VARIABLES LIKE 'validate_password%';

修改密码规则
set global validate_password.length=6;
set global validate_password.policy=0;
```

##### 用户

   ```
1. 创建 新用户gs
create user 'gs'@'%' identified by 'gs';
create user 'gs'@'192.168.72.%' identified by 'gs';
create user 'gs'@'192.168.72.152' identified by 'gs';

2. 修改 用户信息
ALTER USER 'gs'@'19.168.72.%' IDENTIFIED WITH mysql_native_password BY '123456'

3. 给新用户gs赋予test数据库所有权限
grant all privileges  on test.* to "gs"@'%';
   ```

#### 2.2 sql学习

##### 数据库

```
建库
create database `test2`;

删库
drop database if exists test2;

查看库
show databases;

切换库
use test;
```

##### 表

```
建表
CREATE TABLE `test`.`user`  (
  `name` varchar(255) NULL
);

刪表
DROP TABLE if exists user;

查看所有表名
show tables;

查看表结构
desc user;
```





