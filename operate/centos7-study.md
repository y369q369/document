<h1><center>centos7学习过程<center></h1>
### 1. 输入法

#### 1.1 增加输入法 

Settings   --  Region & Language  --  Input Sources    --   + Chinese(Intelligent Pinyin)

#### 1.2 修改输入法 切换快捷键

Settings  --  Devices   --  Keyboard  -- Typing  --  Switch to next input source



### 2. 软件安装

#### 2.1 google浏览器

- 访问官网：http://www.google.cn/chrome/browser/desktop/index.html

- 下载linux版本的npm :   google-chrome-stable_current_x86_64.rpm

- root用户 安装 ： 

  ```
  1. 本地在线安装方式：  自动下载相关依赖  
  	yum localinstall google-chrome-stable_current_x86_64.rpm
  2. 离线安装方式：  缺什么依赖下载什么
  	rpm -ivh google-chrome-stable_current_x86_64.rpm
  ```

- 正常用户启动

  ```
  桌面启动： Applications  --  Internet  -- Google Chrome
  
  命令行启动： /usr/bin/google-chrome --no-sandbox
  ```

- <span style="color:red">root用户 需要在运行时加上 --no-sandbox</span>

  ```
  桌面启动： 用文件夹 打开 /usr/share/applications 目录， 右击 Google Chrome，
  Command 中 末尾添加 --no-sandbox,  即： /usr/bin/google-chrome-stable %U  --no-sandbox
  
  命令行启动： /usr/bin/google-chrome --no-sandbox
  ```

- 参考网站 ：  https://www.linuxidc.com/Linux/2019-09/160524.htm

#### 2.2 docker

1. Docker 要求 CentOS 系统的内核版本高于 3.10

   ```
   uname -r
   ```

2.  使用 root 权限登录 Centos。确保 yum 包更新到最新

   ```
   sudo yum update
   ```

3.  卸载旧版本(如果安装过旧版本的话)

   ```
   sudo yum remove docker  docker-common docker-selinux docker-engine
   ```

4.  安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

   ```
   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

5.  设置yum源（两个选择一个即可）

   ```
   中央仓库：  	sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   阿里仓库：  	sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

6.  可以查看所有仓库中所有docker版本，并选择特定版本安装

   ```
   yum list docker-ce --showduplicates | sort -r
   ```

7.  安装docker

   ```
   sudo yum install docker-ce docker-ce-cli containerd.io    #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版17.12.0
   sudo yum install <FQPN>  # 例如：sudo yum install docker-ce-17.12.0.ce
   ```

8. 启动并加入开机启动

   ```
   启动docker服务
   sudo systemctl start docker
   
   设置开机自动启动docker服务
   sudo systemctl enable docker
   ```

9.  验证安装是否成功(有client和service两部分表示docker安装启动都成功了)

   ```
   docker version
   ```
   
10. 添加阿里云镜像仓库地址

   ```
   [root@localhost ~]# tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://9cpn8tt6.mirror.aliyuncs.com"]
   }
   EOF
   
   [root@localhost ~]# systemctl daemon-reload
   [root@localhost ~]# systemctl restart docker
   ```

11. 给其他用户赋权限

    ```
    1. 创建docker组为附加组， 并添加新用户到docker组且不离开原组
    usermod -a -G docker username
    
    2. 将一个用户添加到某个组
    gpasswd -a username groupname
    将一个用户从某个组删除
    gpasswd -d username groupname
    
    3.重启docker服务
    systemctl restart docker
    ```

- 参考网站 ： https://www.cnblogs.com/wang-yaz/p/10429899.html

#### 2.3 typora - markdown编辑器

1.  访问官网：  https://www.typora.io/

2.  下载 linux 版 的 二进制包， 即 Typora-linux-x64.tar.gz

3.  解压 到 /opt/typora/ 目录下

   ```
   tar -zxvf Typora-linux-x64.tar.gz
   cd bin/
   mkdir /opt/typora
   cp -r Typora-linux-x64/ /opt/typora/
   ```

4.  打开程序

   ```
   正常启动：      /opt/typora/Typora-linux-x64/Typora
   root启动：		/opt/typora/Typora-linux-x64/Typora --no-sandbox
   ```

5. 创建桌面快捷方式

   ```
   1.创建桌面快捷方式文件
   vim Typora.desktop
   
   2.文件内容
   [Desktop Entry]
   Encoding=UTF-8
   Name=Typora
   Exec=/opt/typora/Typora-linux-x64/Typora --no-sandbox
   Terminal=false
   Type=Application
   Icon=/opt/typora/Typora-linux-x64/resources/app/asserts/icon/icon_256x256.png
   StartupNotify=true
   Categories=Development
   
   3. 设置文件可执行
   chmod 755 Typora.desktop
   
   4. 移动到 applications 目录下
   mv Typora.desktop /usr/share/applications/
   ```


#### 2.4 git

- centos7 以上推荐yum安装

- 安装

  ```
  yum install -y git
  ```

- 查看版本

  ```
  git --version
  ```

#### 2.5 curl

- 安装

  ```
  sudo yum install curl
  ```

- 查看版本

  ```
  curl --version
  ```

  

### 3.  网络

#### 3.1 ssh  开放远程连接

1.  查看ssh 是否安装

   ```
   [root@centos7-test ~]# rpm -qa | grep ssh
   libssh2-1.8.0-3.el7.x86_64
   openssh-7.4p1-21.el7.x86_64
   openssh-server-7.4p1-21.el7.x86_64
   openssh-clients-7.4p1-21.el7.x86_64
   ```

2. 如果没有安装ssh服务就安装 

   ```
   yum install openssh-server
   ```

3. 启动ssh服务

   ```
   [root@centos7-test ~]# service sshd start
   Redirecting to /bin/systemctl start  sshd.service
   ```

4. 查看TCP 22端口是否打开

   ```
   [root@centos7-test ~]# netstat -ntpl | grep 22
   tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1331/sshd           
   tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      9114/dnsmasq        
   tcp6       0      0 :::22                   :::*                    LISTEN      1331/sshd
   ```


#### 3.2 nc  查看端口是否连接

1.  检测是否安装

   ```
   [grassprince@centos7-test operate]$ nc --version
   Ncat: Version 7.50 ( https://nmap.org/ncat )
   
   如未安装
   sudo yum install -y nc
   ```

2.  测试连接端口

   ```
   连接成功
   [grassprince@centos7-test operate]$ nc -v 192.168.143.17 8090
   Ncat: Version 7.50 ( https://nmap.org/ncat )
   Ncat: Connected to 192.168.143.17:8090.
   
   连接失败
   [grassprince@centos7-test operate]$ nc -v 192.168.143.17 8090
   Ncat: Version 7.50 ( https://nmap.org/ncat )
   Ncat: Connection refused.
   ```

#### 3.3 telnet  查看端口是否连接

1.  检测是否安装

   ```
   [grassprince@centos7-test operate]$ rpm -qa | grep telnet
   telnet-0.17-64.el7.x86_64
   telnet-server-0.17-64.el7.x86_64
   
   如未安装
   sudo yum install -y telnet-server
   sudo yum install -y telnet
   ```

2. 测试连接端口

   ```
   连接成功
   [grassprince@centos7-test operate]$ telnet 192.168.143.17 8090
   Trying 192.168.143.17...
   Connected to 192.168.143.17.
   Escape character is '^]'.
   
   连接失败
   [grassprince@centos7-test operate]$ telnet 192.168.143.17 8090
   Trying 192.168.143.17...
   telnet: connect to address 192.168.143.17: Connection refused
   
   ```

### 4. 实用操作

#### 4.1 查看java实际使用目录

```
1. 查看java版本
[root@centos7-test bin]# java -version
openjdk version "1.8.0_242"
OpenJDK Runtime Environment (build 1.8.0_242-b08)
OpenJDK 64-Bit Server VM (build 25.242-b08, mixed mode)

2. 查看java执行指令目录
[root@centos7-test bin]# which java
/bin/java

3. 查看java实际目录
[root@centos7-test bin]# ll /bin/java
lrwxrwxrwx. 1 root root 22 Mar 28 03:30 /bin/java -> /etc/alternatives/java
[root@centos7-test bin]# ll /etc/alternatives/java
lrwxrwxrwx. 1 root root 73 Mar 28 03:30 /etc/alternatives/java -> /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64/jre/bin/java
```

#### 4.2 配置 java 环境

```
1.解压 jdk的包 到 /usr/local 目录下
tar -zxvf jdk-8u221-linux-x64.tar.gz -C /usr/local

2. 配置环境变量
vim /etc/profile

export JAVA_HOME=/usr/local/jdk1.8.0_221
export PATH=$JAVA_HOME/bin:$PATH

3.重新加载配置文件
source /etc/profile
```

#### 4.3 修改环境变量

- 当前终端当前用户生效，推出后就没了

  ```
  终端 直接输入
  export TEST=1
  ```

- 当前用户生效

  ```
  1.修改当前用户 根目录 下 的 .bash_profile 或 .bashrc 文件
  vim ~/.bash_profile
  
  2. 末行 添加
  export TEST2=2
  
  3.重新执行刚修改的初始化文件, 或者重启生效
  source ~/.bash_profile
  ```

- 所有用户

  ```
  1.修改 /etc/profile 文件
  vim /etc/profile
  
  2. 末行 添加
  export TEST3=3
  
  3.重新执行刚修改的初始化文件, 或者重启生效
  source /etc/profile
  ```


#### 4.4 设置sudo用户	

- 新建用户

  ```
  useradd -m gs
  ```

- 改密

  ```
  passwd gs
  ```

##### 4.4.1 修改/etc/sudoers文件

- 修改 /etc/sudoers

  ```
  vim /etc/sudoers
  ```

- sudo用户需要密码

  ```
  ## Allow root to run any commands anywhere 
  root    ALL=(ALL)       ALL
  gs      ALL=(ALL)       ALL
  ```

- sudo用户不需密码

  ```
  ## Allow root to run any commands anywhere 
  root    ALL=(ALL)       ALL
  gs      ALL=(ALL)       NOPASSWD:ALL
  ```

##### 4.4.2 添加用户到sudo

- ​	添加用户gs到sudo

  ```
  # 添加sudo用户组
  groupadd sudo
  
  # /etc/sudoers 添加sudo组的免密登录
  %sudo ALL=(ALL) NOPASSWD: ALL
  
  # 第一种  添加用户到用户组
  usermod -a -G sudo gs
  
  # 第二种 添加用户到主要组
  usermod -g sudo gs
  ```

#### 4.5 修改语言

- 

### 5. rpm包下载及安装

> rpm下载管理工具

```
# 安装
yum install -y yum-utils 

# yumdownloader命令的使用
yumdownloader --resolve --destdir=<文件下载地址> <软件名称>
```

> rpm操作

```
# 正常安装
rpm -ivh <rpm包名>
# 无视依赖强制安装
rpm -ivh --force --nodeps <rpm包名>

# 查看
rpm -qa | grep <rpm包名>

# 卸载
rpm -e <rpm包名>
```

