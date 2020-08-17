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
  1. 删除所有rpm包
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
              命令行 wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.21-1.el7.x86_64.rpm-bundle.tar
  ```
  
- 安装 mysql

   ```
1. 解压
  tar -xvf mysql-8.0.21-1.el7.x86_64.rpm-bundle.tar -C ./mysql-community-8
cd mysql-community-8
  
  2. 安装
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

- 设置

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
   ```

- 开机启动

   ```
   systemctl enable mysqld
   systemctl daemon-reload
   ```

#### 1.2 集群

### 2. 新增用户

```
1. 创建 不限ip 的新用户gs
create user 'gs'@'%' identified by 'gs';
2. 给新用户gs赋予test数据库所有权限
grant all privileges  on test.* to "gs"@'%';
```

