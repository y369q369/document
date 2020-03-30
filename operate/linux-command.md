<center><h1>linux指令</h1></center>
### 1. systemd 初始化进程

​	Linux 操作系统的开机过程是这样的，即从 BIOS 开始，然后进入 Boot Loader，再加
载系统内核，然后内核进行初始化，最后启动初始化进程。初始化进程作为 Linux 系统的
第一个进程，它需要完成 Linux 系统中相关的初始化工作，为用户提供合适的工作环境。

#### 1.1 sytstemctl

```
RHEL 6 系统 使用 service、 chkconfig 等命令来管理系统服务
RHEL 7 系统 使用 systemctl 命令来管理服务
```

- systemctl 管理服务的启动、重启、停止、重载、查看状态等常用命令

| System V init （ RHEL 6 系统） | systemctl （ RHEL 7 系统）    | 作用                           |
| ------------------------------ | ----------------------------- | ------------------------------ |
| service foo start              | systemctl start foo.service   | 启动服务                       |
| service foo restart            | systemctl restart foo.service | 重启服务                       |
| service foo stop               | systemctl stop foo.service    | 停止服务                       |
| service foo reload             | systemctl reload foo.service  | 重新加载配置文件（不终止服务） |
| service foo status             | systemctl status foo.service  | 查看服务状态                   |

- 设置服务开机启动、不启动、查看各级别下服务启动状态等常用命令

| System V init （ RHEL 6 系统） | systemctl （ RHEL 7 系统）               | 作用                               |
| ------------------------------ | ---------------------------------------- | ---------------------------------- |
| chkconfig foo on               | systemctl enable foo.service             | 开机自动启动                       |
| chkconfig foo off              | systemctl disable foo.service            | 开机不自动启动                     |
| chkconfig foo                  | systemctl is-enabled foo.service         | 查看特定服务是否为开机自动启动     |
| chkconfig --list               | systemctl list-unit-files --type=service | 查看各个级别下服务的启动与禁用情况 |



### 2. 软件安装及管理

#### 2.1 rpm 包管理器

```
RPM 有点像 Windows 系统中的控制面板，会建立统一的数据库文件，详细记录软件信息并能够自动分析依赖关系
```

- 常用命令

| command               | explanation                     |
| --------------------- | ------------------------------- |
| rpm -ivh filename.rpm | 安装软件的命令格式              |
| rpm -Uvh filename.rpm | 升级软件的命令格式              |
| rpm -e filename.rpm   | 卸载软件的命令格式              |
| rpm -qpi filename.rpm | 查询软件描述信息的命令格式      |
| rpm -qpl filename.rpm | 列出软件文件信息的命令格式      |
| rpm -qf filename      | 查询文件属于哪个 RPM 的命令格式 |

#### 2.2 Yum 软件仓库

```
Yum 软件仓库 可以根据用户的要求分析出所需软件包及其相关的依赖关系，然后自动从服务器下载软件包并安装到系统。
即 解决 rpm 的单一依赖问题
```

- 常用命令

| command                   | explanation                  |
| ------------------------- | ---------------------------- |
| yum repolist all          | 列出所有仓库                 |
| yum list all              | 列出仓库中所有软件包         |
| yum info 软件包名称       | 查看软件包信息               |
| yum install 软件包名称    | 安装软件包                   |
| yum reinstall 软件包名称  | 重新安装软件包               |
| yum update 软件包名称     | 升级软件包                   |
| yum remove 软件包         | 移除软件包                   |
| yum clean all             | 清除所有仓库缓存             |
| yum check-update          | 检查可更新的软件包           |
| yum grouplist             | 查看系统中已经安装的软件包组 |
| yum groupinstall 软件包组 | 安装指定的软件包组           |
| yum groupremove 软件包组  | 移除指定的软件包组           |
| yum groupinfo 软件包组    | 查询指定的软件包组信息       |



### 3.  执行查看帮助命令

```
帮助查看命令的格式，参数等
```

- 常用帮助命令

| command     | explanation         |
| ----------- | ------------------- |
| man 命令    | 查看 命令的完整介绍 |
| 命令 --help | 长格式 的 简单 介绍 |
| 命令 -h     | 短格式 的 简单 介绍 |

- man 命令 帮助界面常用按键

| 按键                  | 用途                               |
| --------------------- | ---------------------------------- |
| 空格键  /   PaGe down | 向下翻一页                         |
| PaGe up               | 向上翻一页                         |
| home                  | 直接前往首页                       |
| end                   | 直接前往尾页                       |
| /                     | 从上至下搜索某个关键词，如“/linux” |
| ?                     | 从下至上搜索某个关键词，如“?linux” |
| n                     | 定位到下一个搜索到的关键词         |
| N                     | 定位到上一个搜索到的关键词         |
| q                     | 退出帮助文档                       |

- man 命令 展示结构

| 结构名称    | 代表意义                 |
| ----------- | ------------------------ |
| NAME        | 命令的名称               |
| SYNOPSIS    | 参数的大致使用方法       |
| DESCRIPTION | 介绍说明                 |
| EXAMPLES    | 演示（附带简单说明）     |
| OVERVIEW    | 概述                     |
| DEFAULTS    | 默认的功能               |
| OPTIONS     | 具体的可用选项（带介绍） |
| ENVIRONMENT | 环境变量                 |
| FILES       | 用到的文件               |
| SEE ALSO    | 相关的资料               |
| HISTORY     | 维护历史与联系方式       |



### 4. 系统状态检测命令

#### 4.1 ifconfig

```
ifconfig 命令用于获取网卡配置与网络状态等信息，其实主要查看的就是网卡名称、 inet 参数后面的 IP 地址、 ether 参数后面的网卡物理地址（又称为 MAC 地址），以及 RX、 TX 的接收数据包与发送数据包的个数及累计流量
```

- 命令格式

  ```
  ifconfig [网络设备] [参数]
  
  一般直接使用 ifconfig 即可
  ```

- 演示案例

```
grassprince@centos7-test ~]$ ifconfig
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:0e:9e:0f:9b  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens32: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.137.136  netmask 255.255.255.0  broadcast 192.168.137.255
        inet6 fe80::b078:87f4:6e3d:7843  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:0e:bc:cc  txqueuelen 1000  (Ethernet)
        RX packets 142322  bytes 188625337 (179.8 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 25426  bytes 5409774 (5.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 32  bytes 2592 (2.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 32  bytes 2592 (2.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:17:5c:3e  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

#### 4.2 uname

```
uname 命令用于查看系统内核与系统版本等信息
```

- 命令格式

  ```
  uname [参数]
  常用 uname -a
  ```

- 查看当前系统版本详细信息

  ```
  redhat / centos :	cat /etc/redhat-release
  ubuntu / suse	:	cat /etc/issue
  ```

- 演示案例

```
[grassprince@centos7-test ~]$ uname -a
Linux centos7-test 3.10.0-1062.18.1.el7.x86_64 #1 SMP Tue Mar 17 23:49:17 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

[grassprince@centos7-test ~]$ cat /etc/redhat-release 
CentOS Linux release 7.7.1908 (Core)
```

#### 4.3 uptime

```
uptime 用于查看系统的负载信息，格式为 uptime
```

- 演示案例

```
[grassprince@centos7-test ~]$ uptime
 08:41:23 up  3:14,  3 users,  load average: 1.56, 1.75, 1.52
显示内容： 当前系统时间、系统已运行时间、启用终端数量以及平均负载值等信息（最近 1 min、5 min、15 min内）
```

#### 4.4 free

```
free 用于显示当前系统中内存的使用量信息, 常用 free -h 人性化展示结果
```

- 演示案例 及说明

|      | 内存总量 | 已用量 | 可用量 | 进程共享的内存量 | 磁盘缓存的内存量 | 缓存的内存量 |
| ---- | -------- | ------ | ------ | ---------------- | ---------------- | ------------ |
|      | total    | used   | free   | shared           | buff/cache       | available    |
| Mem  | 3.7G     | 1.8G   | 644M   | 265M             | 1.2G             | 1.4G         |
| Swap | 3.9G     | 1.5M   | 3.9G   |                  |                  |              |

```
mem：表示物理内存统计
swap：表示硬盘上交换分区的使用情况
```

#### 4.5 who

```
who 用于查看当前登入主机的用户终端信息，格式为 who
```

- 演示案例

```
[grassprince@centos7-test ~]$ who
grassprince :0           2020-03-29 05:27 (:0)
grassprince pts/0        2020-03-29 06:09 (:0)
grassprince pts/1        2020-03-29 06:12 (:0)
```

#### 4.6 last

```
last 命令用于查看所有系统的登录记录， 格式为 last 
注意： 由于这些信息都是以日志文件的形式保存在系统中，因此黑客可以很容易地对内容进行篡改。千万不要单纯以该命令的输出信息而判断系统有无被恶意入侵！
```

- 演示案例

```
[grassprince@centos7-test ~]$ last
grasspri pts/1        :0               Sun Mar 29 06:12   still logged in   
grasspri pts/0        :0               Sun Mar 29 06:09   still logged in   
grasspri :0           :0               Sun Mar 29 05:27   still logged in   
reboot   system boot  3.10.0-1062.18.1 Sun Mar 29 05:26 - 08:56  (03:29)    
grasspri pts/1        :0               Sun Mar 29 02:04 - 02:21  (00:16)    
grasspri pts/1        :0               Sat Mar 28 23:15 - 02:04  (02:48)    
grasspri pts/0        :0               Sat Mar 28 23:11 - 02:05  (02:53)    
grasspri :0           :0               Sat Mar 28 23:09 - 02:21  (03:12)    
reboot   system boot  3.10.0-1062.18.1 Sat Mar 28 23:08 - 08:56  (09:48)    
grasspri pts/0        :0               Sat Mar 28 08:42 - 10:39  (01:57)    
grasspri :0           :0               Sat Mar 28 08:41 - down   (01:57)    
root     pts/0        192.168.137.1    Sat Mar 28 08:36 - 08:41  (00:05)    
root     pts/2        :0               Sat Mar 28 08:06 - 08:22  (00:15)    
root     pts/1        :0               Sat Mar 28 07:54 - 08:40  (00:45)    
root     pts/0        :0               Sat Mar 28 07:52 - 08:22  (00:29)    
root     :0           :0               Sat Mar 28 07:50 - 08:41  (00:50)    
grasspri pts/1        :0               Sat Mar 28 07:40 - 07:50  (00:10)
```

#### 4.7 history

```
history 命令用于显示当前用户历史执行过的命令， 格式为 history
默认1000条， 可通过 /etc/profile 文件 的 HISTSIZE 修改数量
历史路径被保存在 用户家目录 的 .bash_history 中
```

- 演示案例

```
获取历史使用记录
[grassprince@centos7-test ~]$ history
    1  cd /opt/typora/
    2  ls
    3  cd Typora-linux-x64/
    4  ls
    5  ./Typora 
    6  /usr/bin/google-chrome
    7  pwd

重复执行某一条命令：  !编码数字
[grassprince@centos7-test ~]$ !2
ls
Desktop    Downloads    Music     Public     Videos
Documents  gitReposity  Pictures  Templates

清空所有命令历史记录：   history -c
```



### 5. 常用系统工作命令

#### 5.1 echo

```
echo 命令用于在终端输出字符串或变量提取后的值， 格式为 echo [字符串 | $变量]
```

- 演示案例

```
输出字符串
[grassprince@centos7-test document]$ echo Linuxprobe.com
Linuxprobe.com

输出变量
[grassprince@centos7-test document]$ echo $PATH
/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/home/grassprince/.local/bin:/home/grassprince/bin
```

#### 5.2 date

```
date 命令用于显示及设置系统的时间或日期，格式为 “date [选项] [+指定的格式]”。
```

- 演示案例

```
默认格式  查看当前系统时间
[grassprince@centos7-test document]$ date
Mon Mar 30 09:00:13 EDT 2020

按照  “年-月-日 小时:分钟:秒”的格式  查看当前系统时间
[grassprince@centos7-test document]$ date "+%Y-%m-%d %H:%M:%S"
2020-03-30 09:01:35

设置系统时间
[grassprince@centos7-test document]$ sudo date -s "20190101 12:23:40"
Tue Jan  1 12:23:40 EST 2019
```

#### 5.3 reboot

```
reboot 命令用于重启系统，其格式为 reboot。
由于重启计算机这种操作会涉及硬件资源的管理权限，因此默认只能使用 root 管理员来重启
```

#### 5.4  poweroff

```
poweroff 命令用于关闭系统，其格式为 poweroff。
该命令与 reboot 命令相同，都会涉及硬件资源的管理权限，因此默认只有 root 管理员才可以关闭电脑
```

#### 5.5  wget

```
wget 命令用于在终端中下载网络文件，格式为 “wget [参数] 下载地址”。
```

- 常用参数

  | 参数 | 作用                                 |
  | ---- | ------------------------------------ |
  | -b   | 后台下载模式                         |
  | -P   | 下载到指定目录                       |
  | -t   | 最大尝试次数                         |
  | -c   | 断点续传                             |
  | -p   | 下载页面内所有资源，包括图片、视频等 |
  | -r   | 递归下载                             |

- 演示案例

```
下载 linuxprobe 到 /home/grassprince/Downloads/ 目录下
[grassprince@centos7-test ~]$ wget -P /home/grassprince/Downloads/ http://www.linuxprobe.com/docs/LinuxProbe.pdf
--2020-03-30 09:28:55--  http://www.linuxprobe.com/docs/LinuxProbe.pdf
Resolving www.linuxprobe.com (www.linuxprobe.com)... 58.218.215.124
Connecting to www.linuxprobe.com (www.linuxprobe.com)|58.218.215.124|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://www.linuxprobe.com/docs/LinuxProbe.pdf [following]
--2020-03-30 09:28:55--  https://www.linuxprobe.com/docs/LinuxProbe.pdf
Connecting to www.linuxprobe.com (www.linuxprobe.com)|58.218.215.124|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17176352 (16M) [application/pdf]
Saving to: ‘/home/grassprince/Downloads/LinuxProbe.pdf’

100%[======================================>] 17,176,352  7.39MB/s   in 2.2s   

2020-03-30 09:28:57 (7.39 MB/s) - ‘/home/grassprince/Downloads/LinuxProbe.pdf’ saved [17176352/17176352]

查看是否下载
[grassprince@centos7-test ~]$ ll Downloads/
total 16776
-rw-rw-r--. 1 grassprince grassprince 17176352 Mar 24 01:14 LinuxProbe.pdf
```

#### 5.6 ps

```
ps 命令用于查看系统中的进程状态，格式为  ps [参数]
```

- 5中常见进程状态

  | 符号          | 状态说明                                                     |
  | ------------- | ------------------------------------------------------------ |
  | R（运行）     | 进程正在运行或在运行队列中等待                               |
  | S（中断）     | 进程处于休眠中，当某个条件形成后或者接收到信号时，则脱离该状态 |
  | D（不可中断） | 进程不响应系统异步信号，即便用 kill 命令也不能将其中断       |
  | Z（僵死）     | 进程已经终止，但进程描述符依然存在, 直到父进程调用 wait4()系统函数后将进程释放 |
  | T（停止）     | 进程已经终止，但进程描述符依然存在, 直到父进程调用 wait4()系统函数后将进程释放 |

- 演示案例 及 说明

```
[grassprince@centos7-test ~]$ ps aux
```

| 进程的所有者 | 进 程ID 号 | 运算器占用率 | 内 存占用率 | 虚 拟 内 存 使用量（单位是 KB） | 占用的固定内存量（单位是KB） | 所 在终端 | 进程状态 | 被启动的时间 | 实际使用CPU的时间 | 命令名称与参数 |
| ------------ | ---------- | ------------ | ----------- | ------------------------------- | ---------------------------- | --------- | -------- | ------------ | ----------------- | -------------- |
| USER         | PID        | %CPU         | %MEM        | VSZ                             | RSS                          | TTY       | STAT     | START        | TIME              | COMMAND        |
| root         | 1          | 0.3          | 0.0         | 193960                          | 7008                         | ?         | Ss       | 09:26        | 0:01              | /usr/lib/syste |
| root         | 2          | 0.0          | 0.0         | 0                               | 0                            | ?         | S        | 09:26        | 0:00              | [kthreadd]     |
| root         | 3          | 0.0          | 0.0         | 0                               | 0                            | ?         | S        | 09:26        | 0:00              | [kworker/0:0]  |
| root         | 4          | 0.0          | 0.0         | 0                               | 0                            | ?         | S<       | 09:26        | 0:00              | [kworker/0:0H] |
| root         | 6          | 0.0          | 0.0         | 0                               | 0                            | ?         | S        | 09:26        | 0:00              | [ksoftirqd/0]  |
| root         | 7          | 0.0          | 0.0         | 0                               | 0                            | ?         | S        | 09:26        | 0:00              | [migration/0]  |

#### 5.7 top

```
top 命令用于动态地监视进程活动与系统负载等信息，其格式为 top
```

- 演示案例

```
[grassprince@centos7-test ~]$ top
top - 09:47:02 up 20 min,  3 users,  load average: 0.27, 0.44, 0.33
Tasks: 260 total,   1 running, 259 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  1.9 sy,  0.0 ni, 97.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  7990116 total,  4892888 free,  1545176 used,  1552052 buff/cache
KiB Swap:  4063228 total,  4063228 free,        0 used.  5998904 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                    
  2842 grasspr+  20   0 4071284 373580 141596 S   9.6  4.7   2:49.73 gnome-shell                
  2101 root      20   0  560676 242644 138040 S   4.7  3.0   1:05.85 X                          
  3448 grasspr+  20   0  737772  26112  15284 S   2.3  0.3   0:02.53 gnome-terminal-            
  4506 grasspr+  20   0  162140   2392   1580 R   0.7  0.0   0:00.13 top                        
     3 root      20   0       0      0      0 S   0.3  0.0   0:00.60 kworker/0:0
```

- 前五行 代表的含义

```
第 1 行：系统时间、运行时间、登录终端数、系统负载（三个数值分别为 1 min、 5 min、 15 min内的平均值，数值越小意味着负载越低）
第 2 行：进程总数、运行中的进程数、睡眠中的进程数、停止的进程数、僵死的进程数
第 3 行：用户占用资源百分比、系统内核占用资源百分比、改变过优先级的进程资源百分比、空闲的资源百分比等。
		注：第 3 行中的数据均为 CPU 数据并以百分比格式显示，例如“97.1 id”意味着有 97.1%的 
		CPU 处理器资源处于空闲。
第 4 行：物理内存总量、内存使用量、内存空闲量、作为内核缓存的内存量。
第 5 行：虚拟内存总量、虚拟内存使用量、虚拟内存空闲量、已被提前加载的内存量。
```

#### 5.8 pidof

```
pidof 命令用于查询某个指定服务进程的 PID 值，格式为  pidof [参数] [服务名称]
```

- 案例演示

```
查询本机上 sshd 服务程序的 PID
[grassprince@centos7-test ~]$ pidof sshd
1457
```

#### 5.9 kill

```
kill 命令用于终止某个指定 PID 的服务进程，格式为  kill [参数] [进程 PID]
```

- 案例演示

```
终止上一个查出的 sshd 服务
[grassprince@centos7-test ~]$ sudo kill 1457
```

#### 5.10 killall

```
killall 命令用于终止某个指定名称的服务所对应的全部进程，格式为： killall [参数] [进程名称]
通常来讲，复杂软件的服务程序会有多个进程协同为用户提供服务，如果逐个去结束这些进程会比较麻烦，此时可以使用 killall 命令来批量结束某个服务程序带有的全部进程
```

- 案例演示

```
[grassprince@centos7-test ~]$ pidof httpd
13581 13580 13579 13578 13577 13576
[grassprince@centos7-test ~]$ killall httpd
```



### 1. find： 在指定目录下查找文件。

#### 1.1 基本命令

```
find   path  -option  【 -print 】 【 -exec   -ok   |xargs  |grep 】 【  command  {} \; 】
```

#### 1.2 命令参数说明

1）path：要查找的目录路径。 

- ​    ~  表示$HOME目录
- ​     .  表示当前目录
- ​     /  表示根目录 
- ​    不填默认当前目录

2）print：表示将结果输出到标准输出。 

3）exec：对匹配的文件执行该参数所给出的shell命令。 
      形式为command {} \;，注意{}与\;之间有空格 

4）ok：与exec作用相同，
      区别在于，在执行命令之前，都会给出提示，让用户确认是否执行 

5）|xargs  与exec作用相同 ，起承接作用

​      区别在于 |xargs 主要用于承接删除操作 ，而 -exec 都可用 如复制、移动、重命名等

6）options ：表示查找方式

#### 1.3 options常用选项

| 指令选项           | 解释                                                      | 例子             |
| ------------------ | --------------------------------------------------------- | ---------------- |
| -name  filename    | 查找名为filename的文件                                    | find -name *.txt |
| -perm              | 按执行权限来查找                                          | find -perm 644   |
| -user  username    | 按文件属主来查找                                          | find -user root  |
| -group groupname   | 按组来查找                                                | find -group root |
| -mtime  -n +n      | 按文件更改时间来查找文件，-n指n天以内，+n指n天以前        | find -mtime -1   |
| atime  -n +n       | 按文件访问时间来查找文件，-n指n天以内，+n指n天以前        |                  |
| ctime  -n +n       | 按文件创建时间来查找文件，-n指n天以内，+n指n天以前        |                  |
| -nogroup           | 查无有效属组的文件，即文件的属组在/etc/groups中不存在     |                  |
| -nouser            | 查无有效属主的文件，即文件的属主在/etc/passwd中不存在     |                  |
| -type  b/d/c/p/l/f | 查是块设备、目录、字符设备、管道、符号链接、普通文件      | find -type f     |
| -size   n          | 按文件大小查找文件，c:字节,M,k,   +大于， -小于，不写等于 | find -size +13c  |
| -mount             | 查文件时不跨越文件系统mount点                             |                  |
| -follow            | 如果遇到符号链接文件，就跟踪链接所指的文件                |                  |
| -prune             | 忽略某个目录                                              |                  |

#### 1.4 例子

1.  查找大写字母开头的txt文件 

    find . -name '[A-Z]*.txt' -print 

2.  查找用户有写权限或者组用户有写权限的文件或目录 

    find ./ -perm /220
    find ./ -perm /u+w,g+w
    find ./ -perm /u=w,g=w 

3.  查找2天内被更改过的普通文件 

    find . -mtime -2 -type f

4.  查找超过1M的文件 

    find / -size +1M -type f -print 

5.  查找 del.txt 并删除，删除前提示确认 

    find . -name 'del.txt' -ok rm {} \;  



### 2.  ps： 显示当前进程 (process) 的状态

#### 2.1 基本命令

```
ps [options] [--help]
```

#### 2.2  常用指令

  ps  -aux     #显示出系统上的全部进程

  ps  -ef        #显示出系统上的全部进程，且显示出PPID一栏

   ps -ljF        #仅显示与本终端上开启的进程



### 3. grep： 查找文件里符合条件的字符串 

#### 3.1 基本命令 

```
grep [-abcEFGhHilLnqrsvVwxy][-A<显示列数>][-B<显示列数>][-C<显示列数>][-d<进行动作>][-e<范本样式>][-f<范本文件>][--help][范本样式][文件或目录...]
```

#### 3.2 例子

1. 在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行

   grep test *file

2.  反向查找: 查找文件名中包含 test 的文件中不包含test 的行 

   grep -v test *test*

3. 查找进程中包含test的进程

   ps -aux | grep test

#### * Notice

​    grep直接使用查找文件中内容，前面有ps,find等指令过滤前面指令的结果



### 4. vim：文本编辑器

#### 4.1 指令快捷键

| 快捷键 / 命令               | 作用                                           |
| --------------------------- | ---------------------------------------------- |
| ZZ   /   :wq   /   :x       | 保存退出                                       |
| ZQ  /   :q!                 | 不保存退出                                     |
| :w filename                 | 另存为                                         |
| :set number  /  :set  nu    | 显示行号（永久生效需要修改/etc/vim/vimrc文件） |
| :set nonumber  /  :set nonu | 不显示行号                                     |



### 5. chmod/chown： 权限

 ll的输出内容：-rwxr-xr-x  5 root root  94 Jun 27 2017 xdg

```
7种文件类型(第一个-)
		-：普通文件
		d：目录文件
		l： 软链接（类似Windows的快捷方式）
		　　(下面四种是特殊文件)
		b：块设备文件（例如硬盘、光驱等）
		p：管道文件
		c：字符设备文件（例如猫等串口设备）
		s：套接口文件/数据接口文件（例如启动一个MySql服务器时会产生一个mysql.sock文件）
```

文件权限对应关系(rwxr-xr-x ：每三位为一组，分别为u,g,o)

```
r		4		可读
w		2		可读
x		1		可执行
```

chmod执行例子

```
chmod u-r,g+w xdg
chmod u=rw,g=wx,o=rx xdg
chmod a=wx xdg
chmod 755 xdg
```

chown执行例子

```
chown -R   hadoop:hadoop /usr/local/hadoop-2.7.5
```



### 6. firewall： centos7防火墙及端口指令 	

开启8000端口：firewall-cmd --zone=public(作用域) --add-port=8000/tcp(端口和访问类型) --permanent(永久生效)

| 指令介绍           | 指令例子                                                     |
| ------------------ | ------------------------------------------------------------ |
| 开启防火墙         | systemctl start firewalld                                    |
| 查看状态           | firewall-cmd --state                                         |
| 查看已开放的端口   | firewall-cmd --list-ports                                    |
| 开启端口           | firewall-cmd --zone=public --add-port=1521/tcp --permanent   |
| 关闭端口           | firewall-cmd --zone= public --remove-port=8000/tcp --permanent |
| 重启防火墙         | firewall-cmd --reload                                        |
| 停止防火墙         | systemctl stop firewalld.service                             |
| 禁止防火墙开机启动 | systemctl disable firewalld.service                          |

