<center><h1>linux指令</h1></center>

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