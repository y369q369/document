<h1><center>git-study</center></h1>
### 1. 下载安装

#### 1.1 win10

- git

```
官网地址:   https://git-scm.com/download/win
镜像地址:   https://npm.taobao.org/mirrors/git-for-windows/
```

- GitHub Desktop

```
官网地址:   https://desktop.github.com/
```

#### 1.2 centos

```
在线安装:     yum install -y git
```

### 2. 全局配置

#### 2.1 配置ssh密匙(windows)

- gitlab（账号一）

  1.  生成密匙

     ```
     1. 命令          ssh-keygen -t rsa -C "qing.yang@transwarp.cn"
     2. 修改文件名     当出现 Enter file in which to save the key  ->  输入要生成的文件名
     3. 实际命令行显示   
     $ ssh-keygen -t rsa -C "qing.yang@transwarp.cn"
     Generating public/private rsa key pair.
     Enter file in which to save the key (/c/Users/grassprince/.ssh/id_rsa): id_rsa_gitlab
     Enter passphrase (empty for no passphrase):
     Enter same passphrase again:
     Your identification has been saved in id_rsa_gitlab.
     Your public key has been saved in id_rsa_gitlab.pub.
     The key fingerprint is:
     SHA256:p7cW8sBiT4BWmCucKVJL+KcoPTtLqVhG/JGYtpzHIDw qing.yang@transwarp.cn
     The key's randomart image is:
     +---[RSA 3072]----+
     | .   o           |
     |. o o .          |
     | = + +           |
     |+.Bo=..          |
     |oEB=o  oS .      |
     |o==B .o =o.      |
     |. Oo+. +.+..     |
     |.=o.    ..o.     |
     |o .o     ..      |
     +----[SHA256]-----+
     ```

  2.  添加ssh

     ```
     ssh-agent bash
     
     ssh-add ~/.ssh/id_rsa_gitlab 			//linux用这个
     ssh-add id_rsa_gitlab 					//windows用这个
     ```

  3.  配置

     ```
     1. /c/Users/grassprince/.ssh 目录下 新建config文件
        touch config
     2. 添加内容
     
     #如GitLab
     Host gitlab.com
     	HostName gitlab.com
     	IdentityFile c:\Users\grassprince\.ssh\id_rsa_gitlab
     ```

  4.  连接gitlab

     ```
     1. 复制 /c/Users/grassprince/.ssh 目录下 id_rsa_gitlab.pub 文件内容 到 gitlab添加ssh的页面(https://gitlab.com/profile/keys)上， 可修改title
     2. 测试连接
     $ ssh -T git@gitlab.com
     The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
     ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
     Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
     Warning: Permanently added 'gitlab.com,172.65.251.78' (ECDSA) to the list of known hosts.
     ```

-  github(windows)

  1.  生成密匙

     ```
     1. 命令          ssh-keygen -t rsa -C "1486866853@qq.com"
     2. 修改文件名     当出现 Enter file in which to save the key  ->  输入要生成的文件名
     3. 实际命令行显示   
     $ ssh-keygen -t rsa -C "1486866853@qq.com"
     Generating public/private rsa key pair.
     Enter file in which to save the key (/c/Users/grassprince/.ssh/id_rsa): id_rsa_github
     Enter passphrase (empty for no passphrase):
     Enter same passphrase again:
     Your identification has been saved in id_rsa_github.
     Your public key has been saved in id_rsa_github.pub.
     The key fingerprint is:
     SHA256:98gHJdMILhMz5rxJJ2MOpgff2pZ5/fqEBSPAB7r2IHo 1486866853@qq.com
     The key's randomart image is:
     +---[RSA 3072]----+
     |     .B..        |
     |     =.*.. o     |
     |  . + X.+ * o    |
     |   = B O . *     |
     |  o * = S o .    |
     | . + = o + *     |
     |. E . * . = +    |
     | .   . .   +     |
     |          .oo    |
     +----[SHA256]-----+
     ```

  2.  添加ssh

     ```
     ssh-agent bash
     
     ssh-add ~/.ssh/id_rsa_github 			//linux用这个
     ssh-add id_rsa_github 					//windows用这个
     ```

  3.  配置

     ```
     1. 修改 /c/Users/grassprince/.ssh 目录下 的config文件
     
     #如GitHub
     Host github
     	HostName github.com
     	IdentityFile c:\Users\grassprince\.ssh\id_rsa_github
     ```

  4.  连接github

     ```
     1. 复制 /c/Users/grassprince/.ssh 目录下 id_rsa_github.pub 文件内容 到 gitlab添加ssh的页面(https://github.com/settings/ssh/new)上， 可修改title
     2. 测试连接
     $ ssh -T git@github.com
     The authenticity of host 'github.com (52.74.223.119)' can't be established.
     RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
     Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
     Warning: Permanently added 'github.com,52.74.223.119' (RSA) to the list of known hosts.
     Hi y369q369! You've successfully authenticated, but GitHub does not provide shell access.
     ```

-  参考网址：   https://blog.csdn.net/u010132177/article/details/104825446/ 

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

