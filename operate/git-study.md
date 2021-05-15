<h1><center>git-study</center></h1>
### 1. 下载安装

#### 1.1 win10

> `git`

官网地址:   https://git-scm.com/download/win
镜像地址:   https://npm.taobao.org/mirrors/git-for-windows/

> `GitHub Desktop`

官网地址:   https://desktop.github.com/

#### 1.2 centos

> `git`

在线安装:     yum install -y git

### 2. 全局配置

#### 2.1 配置ssh密匙(windows)

##### 2.1.1 gitlab

> 1、生成密匙

```bash
# 命令          
$ ssh-keygen -m PEM -t rsa -b 4096 -C "qing.yang@transwarp.cn" -f /c/Users/gs/.ssh/id_rsa_gitlab

# 实际命令行显示   
$ ssh-keygen -m PEM -t rsa -b 4096 -C "qing.yang@transwarp.cn" -f /c/Users/gs/.ssh/id_rsa_gitlab
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/gs/.ssh/id_rsa_gitlab.
Your public key has been saved in /c/Users/gs/.ssh/id_rsa_gitlab.pub.
The key fingerprint is:
SHA256:TdRvSiny5IJlV1cCOhomuabdNK8zMNiC8LaqD9e2Ppw qing.yang@transwarp.cn
The key's randomart image is:
+---[RSA 4096]----+
|          .......|
|       . . .o .. |
|      o o +. +   |
|.      ++=+.o o  |
| o . oo+S*.o o   |
|  +.o+=o.oo .    |
|...o+o.o...      |
| o..E.  o.       |
|+o..o.  .o       |
+----[SHA256]-----+

```

> 2、配置

> /c/Users/yq/.ssh 目录下  新建config文件

> # 添加内容

```
# 如GitLab
Host gitlab.com
HostName gitlab.com
PreferredAuthentications publickey
IdentityFile c:\Users\gs\.ssh\id_rsa_gitlab
```

> 3、连接gitlab

```bash
# gitlab上添加私匙
复制 /c/Users/grassprince/.ssh 目录下 id_rsa_gitlab.pub 文件内容 到 gitlab添加ssh的页面(https://gitlab.com/profile/keys)上， 可修改title

# 测试连接
$ ssh -T git@gitlab.com
The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'gitlab.com,172.65.251.78' (ECDSA) to the list of known hosts.
```

##### 2.1.2 github

> 1、生成密匙

```bash
# 命令          
ssh-keygen -m PEM -t rsa -b 4096 -C "1486866853@qq.com" -f /c/Users/gs/.ssh/id_rsa_github

# 实际命令行显示(未设置密码)   
$ ssh-keygen -m PEM -t rsa -b 4096 -C "1486866853@qq.com" -f /c/Users/gs/.ssh/id_rsa_github
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/gs/.ssh/id_rsa_github.
Your public key has been saved in /c/Users/gs/.ssh/id_rsa_github.pub.
The key fingerprint is:
SHA256:KAee5NYLKZazfI3ORF+dD14y2A++ZQ67MtW1rwZEkMk 1486866853@qq.com
The key's randomart image is:
+---[RSA 4096]----+
|         ..+     |
|          E .    |
|    o      .     |
|   = = . + .. .  |
|  = X + S Oo.. . |
| o * B o o.Oo .  |
|  o + +  .+ =. . |
|   =    o  B  . .|
|    o    o+..... |
+----[SHA256]-----+

```

> 2、配置

> 修改 /c/Users/yq/.ssh 目录下 的config文件

```
# 如GitHub
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile c:\Users\gs\.ssh\id_rsa_github
```

> 3、连接github

```bash
# gitlab上添加私匙
复制 /c/Users/grassprince/.ssh 目录下 id_rsa_github.pub 文件内容 到 gitlab添加ssh的页面(https://github.com/settings/ssh/new)上， 可修改title

# 测试连接
$ ssh -T git@github.com
The authenticity of host 'github.com (52.74.223.119)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,52.74.223.119' (RSA) to the list of known hosts.
 Hi y369q369! You've successfully authenticated, but GitHub does not provide shell access.
```

##### 2.1.3 参考

> 参考网址：   https://blog.csdn.net/u010132177/article/details/104825446/ 


#### 2.2 git网站上图片无法显示

> 原因

DNS污染 ： **网域服务器缓存污染**（DNS cache pollution），又称**域名服务器缓存投毒**（DNS cache poisoning），是指一些刻意制造或无意中制造出来的域名服务器数据包，把域名指往不正确的IP地址。一般来说，在互联网上都有可信赖的网域服务器，但为减低网络上的流量压力，一般的域名服务器都会把从上游的域名服务器获得的解析记录暂存起来，待下次有其他机器要求解析域名时，可以立即提供服务。一旦有关网域的局域域名服务器的缓存受到污染，就会把网域内的计算机导引往错误的服务器或服务器的网址。 

> 解决方法

配置hosts

```
#windows： c:\windows\system32\drivers\etc
199.232.68.133 raw.githubusercontent.com
199.232.68.133 githubusercontent.com
```

#### 2.2 配置全局用户名和邮箱

```bash
# 用户名
git config  --global user.name y369q369

# 邮箱
git config  --global user.email 1486866853@qq.com
```
### 3. 常用命令

#### 3.1 常规操作

| 命令                             | 说明                 | 例                                                       |
| -------------------------------- | -------------------- | -------------------------------------------------------- |
| git clone 项目地址               | 下载项目             |                                                          |
| git branch -a                    | 查看所有分支         |                                                          |
| git checkout 当前分支  -b 新分支 | 基于某分支创建新分支 | git checkout remotes/origin/master -b yq                 |
| git remote -v                    | 查看项目远程url      |                                                          |
| git remote rm origin             | 删除项目远程url      |                                                          |
| git remote add origin 远程url    | 添加项目的远程url    | git remote add origin git@github.com:cc-lcek/edu-vue.git |

#### 3.2 修改文件操作

| 命令                 | 说明                        | 例                              |
| -------------------- | --------------------------- | ------------------------------- |
| git status           | 查看修改文件                |                                 |
| git add 文件名       | 添加修改的文件              |                                 |
| git rm 文件名        | 删除文件                    |                                 |
| git commit -m '内容' | 本地提交修改并添加注释      |                                 |
| git push             | 提交修改到github账户        |                                 |
| git merge 指定分支   | 与dev分支合并(当前分支为主) | git merge remotes/origin/master |

#### 3.3 还原某个文件

```
1. 查找某个文件本地的提交记录
git log operate/mysql-study.md
commit 244e5cc3a50e2ab2aa15b2860a081814181b5061 (HEAD -> master, origin/master)
Author: y369q369 <1486866853@qq.com>
Date:   Mon Aug 17 17:26:31 2020 +0800

2. 还原
git checkout 244e5cc3a50e2ab2aa15b2860a081814181b5061 operate/mysql-study.md
```

#### 3.4 加快下载速度

```
原           git clone https://github.com/PanJiaChen/vue-element-admin.git
改           git clone https://github.com.cnpmjs.org/PanJiaChen/vue-element-admin.git
修改远程url	  git remote set-url origin https://github.com/PanJiaChen/vue-element-admin.git

操作： https前缀  https://github.com    ->  https://github.com.cnpmjs.org

```

