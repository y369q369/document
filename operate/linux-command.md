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



### 6. 工作目录切换命令

#### 6.1 pwd

```
pwd 命令用于显示用户当前所处的工作目录，格式为  pwd [选项]
```

- 案例演示

```
[grassprince@centos7-test ~]$ pwd
/home/grassprince
```

#### 6.2 cd 

```
cd 命令用于切换工作路径，格式为   cd [目录名称]
```

- 特殊符号

  | command      | explanation                |
  | ------------ | -------------------------- |
  | cd ..        | 上一级目录                 |
  | cd -         | 返回上一次目录             |
  | cd ~         | 切换到当前用户的home目录下 |
  | cd ~username | 切换到某用户的home目录下   |

- 案例演示

```
切换到 /bin 目录
[grassprince@centos7-test ~]$ cd /bin/
[grassprince@centos7-test bin]$ pwd
/bin
```

#### 6.3 ls

```
ls 命令用于显示目录中的文件信息，格式为   ls [选项] [文件] 
```

- 案例演示

```
显示正常文件
[grassprince@centos7-test ~]$ ls
Desktop    Downloads    Music     Public     Videos
Documents  gitReposity  Pictures  Templates

显示所有文件（包含隐藏文件）
[grassprince@centos7-test ~]$ ls -a
.              .bashrc  Documents    .ICEauthority  .pki
..             .cache   Downloads    .local         Public
.bash_history  .config  .esd_auth    .mozilla       Templates
.bash_logout   .dbus    .gitconfig   Music          Videos
.bash_profile  Desktop  gitReposity  Pictures       .viminfo

显示正常文件并输出这些文件的属性信息， ll 等同于 ls -l
[grassprince@centos7-test ~]$ ll
total 0
drwxr-xr-x. 2 grassprince grassprince 84 Mar 29 01:50 Desktop
drwxr-xr-x. 2 grassprince grassprince 28 Mar 29 01:46 Documents
drwxr-xr-x. 2 grassprince grassprince 28 Mar 30 09:28 Downloads
drwxrwxr-x. 3 grassprince grassprince 22 Mar 28 23:43 gitReposity
drwxr-xr-x. 2 grassprince grassprince  6 Mar 28 07:38 Music
drwxr-xr-x. 2 grassprince grassprince  6 Mar 28 07:38 Pictures
drwxr-xr-x. 2 grassprince grassprince  6 Mar 28 07:38 Public
drwxr-xr-x. 2 grassprince grassprince  6 Mar 28 07:38 Templates
drwxr-xr-x. 2 grassprince grassprince  6 Mar 28 07:38 Videos
```



### 7. 文本文件编辑命令

#### 7.1 cat

```
cat 命令用于查看纯文本文件（内容较少的），格式为   cat [选项] [文件]
```

- 案例演示

```
显示.gitconfig文件信息
[grassprince@centos7-test ~]$ cat .gitconfig 
[user]
	email = 1486866853@qq.com
[push]
	default = simple
	
显示.gitconfig文件信息（含行号）	
[grassprince@centos7-test ~]$ cat -n .gitconfig
     1	[user]
     2		email = 1486866853@qq.com
     3	[push]
     4		default = simple

```

#### 7.2 more

```
more 命令用于查看纯文本文件（内容较多的），格式为   more [选项]文件
```

- 案例演示

```
查看 linux-command.md 文件，  可用 空格 和 回车 向下翻页
[grassprince@centos7-test ~]$ more gitReposity/document/operate/linux-command.md 
<center><h1>linux指令</h1></center>
### 1. systemd 初始化进程

	Linux 操作系统的开机过程是这样的，即从 BIOS 开始，然后进入 Boot Loader，
再加
载系统内核，然后内核进行初始化，最后启动初始化进程。初始化进程作为 Linux 系统的
第一个进程，它需要完成 Linux 系统中相关的初始化工作，为用户提供合适的工作环境。

#### 1.1 sytstemctl

​```
RHEL 6 系统 使用 service、 chkconfig 等命令来管理系统服务
RHEL 7 系统 使用 systemctl 命令来管理服务
​```

- systemctl 管理服务的启动、重启、停止、重载、查看状态等常用命令

| System V init （ RHEL 6 系统） | systemctl （ RHEL 7 系统）    | 作用         
                  |
| ------------------------------ | ----------------------------- | -------------
----------------- |
| service foo start              | systemctl start foo.service   | 启动服务     
                  |
--More--(3%)
```

#### 7.3 head

```
head 命令用于查看纯文本文档的前 N 行，格式为   head [选项] [文件]
```

- 案例演示

```
查看 文件 的前十行
[grassprince@centos7-test ~]$ head -n 10 gitReposity/document/operate/linux-command.md 
<center><h1>linux指令</h1></center>
### 1. systemd 初始化进程

	Linux 操作系统的开机过程是这样的，即从 BIOS 开始，然后进入 Boot Loader，再加
载系统内核，然后内核进行初始化，最后启动初始化进程。初始化进程作为 Linux 系统的
第一个进程，它需要完成 Linux 系统中相关的初始化工作，为用户提供合适的工作环境。

#### 1.1 sytstemctl

​```
```

#### 7.4 tail

```
tail 命令用于查看纯文本文档的后 N 行或持续刷新内容，格式为   tail [选项] [文件]
```

- 案例演示

```
查看 文档 的后十行
[grassprince@centos7-test ~]$ tail -n 10 gitReposity/document/operate/linux-command.md 
| ------------------ | ------------------------------------------------------------ |
| 开启防火墙         | systemctl start firewalld                                    |
| 查看状态           | firewall-cmd --state                                         |
| 查看已开放的端口   | firewall-cmd --list-ports                                    |
| 开启端口           | firewall-cmd --zone=public --add-port=1521/tcp --permanent   |
| 关闭端口           | firewall-cmd --zone= public --remove-port=8000/tcp --permanent |
| 重启防火墙         | firewall-cmd --reload                                        |
| 停止防火墙         | systemctl stop firewalld.service                             |
| 禁止防火墙开机启动 | systemctl disable firewalld.service                          |

查看 文档 并刷新(不加行数默认显示10行)
[grassprince@centos7-test ~]$ tailf gitReposity/document/operate/linux-command.md 
| ------------------ | ------------------------------------------------------------ |
| 开启防火墙         | systemctl start firewalld                                    |
| 查看状态           | firewall-cmd --state                                         |
| 查看已开放的端口   | firewall-cmd --list-ports                                    |
| 开启端口           | firewall-cmd --zone=public --add-port=1521/tcp --permanent   |
| 关闭端口           | firewall-cmd --zone= public --remove-port=8000/tcp --permanent |
| 重启防火墙         | firewall-cmd --reload                                        |
| 停止防火墙         | systemctl stop firewalld.service                             |
| 禁止防火墙开机启动 | systemctl disable firewalld.service                          |

```

#### 7.5 tr

```
tr 命令用于替换文本文件中的字符，格式为    tr [原始字符] [目标字符]
```

- 案例演示

```
将 .bash_profile 文件展示， 并将 所有小写转为大写
[grassprince@centos7-test ~]$ cat .bash_profile | tr [a-z] [A-Z]
# .BASH_PROFILE

# GET THE ALIASES AND FUNCTIONS
IF [ -F ~/.BASHRC ]; THEN
	. ~/.BASHRC
FI

# USER SPECIFIC ENVIRONMENT AND STARTUP PROGRAMS

PATH=$PATH:$HOME/.LOCAL/BIN:$HOME/BIN

EXPORT PATH
```

#### 7.6 wc

```
wc 命令用于统计指定文本的行数、字数、字节数，格式为   wc [参数] 文本
```

- 参数说明

  | 参数 | 作用         |
  | ---- | ------------ |
  | -l   | 只显示行数   |
  | -w   | 只显示单词数 |
  | -c   | 只显示字节数 |

- 案例演示

```
显示 /etc/passwd 文件中的单词数
[grassprince@centos7-test ~]$ wc -w /etc/passwd
90 /etc/passwd
```

#### 7.7 stat

```
stat 命令用于查看文件的具体存储信息和时间等信息，格式为  stat 文件名称
```

- 案例演示

```
[grassprince@centos7-test ~]$ stat gitReposity/document/operate/linux-command.md 
  File: ‘gitReposity/document/operate/linux-command.md’
  Size: 36235     	Blocks: 72         IO Block: 4096   regular file
Device: fd02h/64770d	Inode: 16893873    Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/grassprince)   Gid: ( 1000/grassprince)
Context: unconfined_u:object_r:user_home_t:s0
Access: 2020-04-01 09:01:57.347572334 -0400
Modify: 2020-04-01 09:03:04.943595109 -0400
Change: 2020-04-01 09:03:04.943595109 -0400
 Birth: -
```

#### 7.8 cut

```
cut 命令用于按“列”提取文本字符，格式为   cut [参数] 文本
```

- 案例演示

```
提取 etc/passwd 文件 ：分隔 的 第一列数据
[grassprince@centos7-test ~]$ head -n 3 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
[grassprince@centos7-test ~]$ cut -d: -f1 /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
```



#### 7.9 diff

```
diff 命令用于比较多个文本文件的差异，格式为   diff [参数] 文件
```

- 案例演示

```
通过 -q / --brief 参数查看两个文件是否相同
[grassprince@centos7-test ~]$ diff -q gitReposity/document/operate/jmeter.txt gitReposity/document/operate/ubutun.txt 
Files gitReposity/document/operate/jmeter.txt and gitReposity/document/operate/ubutun.txt differ

通过 -c 参数查看两个文件具体差异
[grassprince@centos7-test ~]$ diff -c gitReposity/document/operate/jmeter.txt gitReposity/document/operate/ubutun.txt 
*** gitReposity/document/operate/jmeter.txt	2020-03-28 23:44:37.292978041 -0400
--- gitReposity/document/operate/ubutun.txt	2020-03-28 23:44:37.292978041 -0400
***************
*** 1,3 ****
! jmeter
! 安装：		https://blog.csdn.net/wust_lh/article/details/86095924
! 使用：		https://blog.csdn.net/yaorongke/article/details/82799609
\ No newline at end of file
--- 1,51 ----
! ubutun
! 密码：123987
```

### 8. 文件目录管理命令

#### 8.1 touch

```
touch 命令用于创建空白文件或设置文件的时间，格式为   touch [选项] [文件]
```

- 常用参数说明

  | 参数 | 作用                      |
  | ---- | ------------------------- |
  | -a   | 仅修改 "读取时间" (atime) |
  | -m   | 仅修改 "修改时间" (mtime) |
  | -d   | 同时修改 atime 与 mtime   |

- 案例演示

```
创建a.txt,  并  修改 读取和修改时间
[grassprince@centos7-test Templates]$ touch a.txt
[grassprince@centos7-test Templates]$ ll a.txt 
-rw-rw-r--. 1 grassprince grassprince 0 Apr  1 09:26 a.txt
[grassprince@centos7-test Templates]$ touch -d '2020-04-20 13:14:20' a.txt 
[grassprince@centos7-test Templates]$ ll a.txt 
-rw-rw-r--. 1 grassprince grassprince 0 Apr 20  2020 a.txt
```

#### 8.2 mkdir

```
mkdir 命令用于创建空白的目录，格式为   mkdir [选项] 目录
```

- 案例演示

```
-p 可递归创建文件夹，  不加 参数 直接创建
[grassprince@centos7-test Templates]$ mkdir test
[grassprince@centos7-test Templates]$ ll
total 0
-rw-rw-r--. 1 grassprince grassprince 0 Apr 20  2020 a.txt
drwxrwxr-x. 2 grassprince grassprince 6 Apr  1 09:29 test
[grassprince@centos7-test Templates]$ mkdir -p test/test1/test2
[grassprince@centos7-test Templates]$ ll test/test1/test2/
total 0
[grassprince@centos7-test Templates]$ ll test/test1/
total 0
drwxrwxr-x. 2 grassprince grassprince 6 Apr  1 09:29 test2
```

#### 8.3 cp

```
cp 命令用于复制文件或目录，格式为   cp [选项] 源文件 目标文件
```

- 常用参数说明

  | 参数 | 作用                                         |
  | ---- | -------------------------------------------- |
  | -p   | 保留原始文件的属性                           |
  | -d   | 若对象为“链接文件”，则保留该“链接文件”的属性 |
  | -r   | 递归持续复制（用于目录）                     |
  | -i   | 若目标文件存在则询问是否覆盖                 |
  | -a   | 相当于-pdr（ p、 d、 r 为上述参数            |

- 案例演示

```
[grassprince@centos7-test Templates]$ ls tes/
[grassprince@centos7-test Templates]$ cp a.txt tes/
[grassprince@centos7-test Templates]$ ls tes/
a.txt
```

#### 8.4 mv

```
mv 命令用于剪切文件或将文件重命名， 格式为  mv [选项] 源文件 [目标路径|目标文件名]
```

- 案例演示

```
重命名 a.txt 为 b.txt
[grassprince@centos7-test Templates]$ ls
a.txt  tes
[grassprince@centos7-test Templates]$ mv a.txt b.txt
[grassprince@centos7-test Templates]$ ls
b.txt  tes
```

#### 8.5 rm

```
rm 命令用于删除文件或目录，格式为   rm [选项] 文件
```

- 案例演示

```
删除目录 tes，  -r 递归删除  -f 提示默认为 确定
[grassprince@centos7-test Templates]$ ls
b.txt  tes
[grassprince@centos7-test Templates]$ rm -rf tes/
[grassprince@centos7-test Templates]$ ls
b.txt
```

#### 8.6 dd

```
dd 命令用于按照指定大小和个数的数据块来复制文件或转换文件，格式为    dd [参数]
```

- 参数说明

  | 参数  | 作用                 |
  | ----- | -------------------- |
  | if    | 输入的文件名称       |
  | of    | 输出的文件名称       |
  | bs    | 设置每个“块”的大小   |
  | count | 设置要复制“块”的个数 |

- 案例演示

```
把光驱设备中的光盘制作成 iso 格式的镜像文件
[root@linuxprobe ~]# dd if=/dev/cdrom of=RHEL-server-7.0-x86_64-LinuxProbe.Com.iso
7311360+0 records in
7311360+0 records out
3743416320 bytes (3.7 GB) copied, 370.758 s, 10.1 MB/s
```

#### 8.7 file

```
file 命令用于查看文件的类型，格式为    file 文件名
```

- 案例演示

```
[grassprince@centos7-test Templates]$ file test/
test/: directory
[grassprince@centos7-test Templates]$ file b.txt 
b.txt: empty
[grassprince@centos7-test Templates]$ file /dev/sda
/dev/sda: block special
```

### 9. 打包压缩与搜索命令

#### 9.1 tar

```
tar 命令用于对文件进行打包压缩或解压，格式为     tar [选项] [文件]
```

- 常用参数及作用

  | 参数 | 作用                   |
  | ---- | ---------------------- |
  | -c   | 创建压缩文件           |
  | -x   | 解开压缩文件           |
  | -t   | 查看压缩包内有哪些文件 |
  | -z   | 用 Gzip 压缩或解压     |
  | -j   | 用 bzip2 压缩或解压    |
  | -v   | 显示压缩或解压的过程   |
  | -f   | 目标文件名             |
  | -p   | 保留原始的权限与属性   |
  | -P   | 使用绝对路径来压缩     |
  | -C   | 指定解压到的目录       |

- 案例演示

```
创建压缩文件 test2.tar.gz
[grassprince@centos7-test Templates]$ tar -zcvf test2.tar.gz test/
test/
test/b.txt

不解压查看压缩文件内容
[grassprince@centos7-test Templates]$ tar tvf test2.tar.gz 
drwxrwxr-x grassprince/grassprince 0 2020-04-03 13:35 test/
-rw-rw-r-- grassprince/grassprince 0 2020-04-20 13:14 test/b.txt

解压文件 test2.tar.gz 到 test/ 目录下
[grassprince@centos7-test Templates]$ tar -zxvf test2.tar.gz -C test/
test/
test/b.txt
tar: test/b.txt: time stamp 2020-04-20 13:14:20 is 1467083.803376688 s in the future
```

#### 9.2 grep

```
grep 命令用于在文本中执行关键词搜索， 并显示匹配的结果， 格式为   grep [选项] [文件]
```

- 常用参数及作用

  | 参数 | 作用                                               |
  | ---- | -------------------------------------------------- |
  | -b   | 将可执行文件（ binary）当作文本文件（ text）来搜索 |
  | -c   | 仅显示找到的行数                                   |
  | -i   | 忽略大小写                                         |
  | -n   | 显示行号                                           |
  | -v   | 反向选择—仅列出没有“关键词”的行                    |

- 案例演示

```
查找进程 ：  PICK 忽略大小写，显示行号
[grassprince@centos7-test Templates]$ ps aux | grep -i -n PICK
270:postfix   12977  0.0  0.0 102052  4084 ?        S    13:13   0:00 pickup -l -t unix -u
291:grasspr+  13960  0.0  0.0 112716   988 pts/0    S+   13:47   0:00 grep --color=auto -i -n PICK
```

#### 9.3 find

```
find 命令用于按照指定条件来查找文件，格式为   
	find path -option 【 -print 】【 -exec  -ok   |xargs  |grep 】【 command  {} \; 】
```

- 格式说明

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

- 常用参数及作用

  | 参数               | 作用                                                         |
  | ------------------ | ------------------------------------------------------------ |
  | -name              | 匹配名称                                                     |
  | -perm              | 匹配权限（ mode 为完全匹配， -mode 为包含即可）              |
  | -user              | 匹配所有者                                                   |
  | -group             | 匹配所有组                                                   |
  | -mtime   -n  +n    | 匹配修改内容的时间（ -n 指 n 天以内， +n 指 n 天以前）       |
  | -atime  -n +n      | 匹配访问文件的时间（ -n 指 n 天以内， +n 指 n 天以前）       |
  | -ctime  -n +n      | 匹配修改文件权限的时间（ -n 指 n 天以内， +n 指 n 天以前）   |
  | -nouser            | 匹配无所有者的文件                                           |
  | -nogroup           | 匹配无所有组的文件                                           |
  | -newer f1 !f2      | 匹配比文件 f1 新但比 f2 旧的文件                             |
  | --type b/d/c/p/l/f | 匹配文件类型（后面的字幕参数依次表示块设备、目录、字符设备、管道、链接文件、文本文件） |
  | -size              | 匹配文件的大小（ +50KB 为查找超过 50KB 的文件，而-50KB 为查找小于50KB 的文件） |
  | -prune             | 忽略某个目录                                                 |

- 案例演示

```
1. 查找大写字母开头的txt文件 
   find . -name '[A-Z]*.txt' -print 

2. 查找用户有写权限或者组用户有写权限的文件或目录 
   find ./ -perm /220
   find ./ -perm /u+w,g+w
   find ./ -perm /u=w,g=w 

3. 查找2天内被更改过的普通文件 
   find . -mtime -2 -type f

4. 查找超过1M的文件 
   find / -size +1M -type f -print 

5. 查找 del.txt 并删除，删除前提示确认 
   find . -name 'del.txt' -ok rm {} \;  
```



### 10. shell脚本流程控制语句

#### 10.1 if

```
1. 单分支判断
if 条件判断
	then 执行操作
fi

2. 双分支判断
if 条件判断
	then 执行操作1
	else 执行操作2
fi 

3. 多分枝判断
if 条件判断1
	then 执行操作1
elif 条件判断2
	then 执行操作2
else 执行操作3
fi 
```

#### 10.2 for

```
for 变量名 in 取值列表
do 执行操作
done
```

#### 10.3 while （可用 exit 0 强行退出）

```
while 条件判断
do 执行操作
done
```

#### 10.4 case

```
case 变量值 in
值1)
	执行操作1
	;;
值2)
	执行操作2
	;;
	......
*)
	执行默认操作
esac
```



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

