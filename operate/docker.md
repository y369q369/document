<h1><center>docker</center></h1>
### 1.  命令

| docker pull   |           function            |
| :------------ | :---------------------------: |
| docker pull   |        获取image(镜像)        |
| docker build  |           创建image           |
| docker images |           列出image           |
| docker run    |      运行container(容器)      |
| docker ps     |         列出container         |
| docker rm     |         删除container         |
| docker rmi    |           删除image           |
| docker cp     | 在host和container之间拷贝文件 |
| docker commit |      保存改动为新的image      |

### 2. 基础实操例子

-  命令帮助

```
docker command --help   如：docker stats --help
```

- 本地没有镜像，拉取镜像

```
docker pull 镜像    如：   docker pull ubuntu
```

- 运行容器

```
winpty docker run -it ubuntu bash  (可交命令行运行)
winpty docker run -itd ubuntu bash (后台运行)

winpty docker run --name testName -itd ubuntu bash (指定容器name)
```

- 查看容器

```
docker ps      (当前运行容器)
docker ps -a   (当前停止运行的容器)
```

- 把停止的容器重启

```
docker start 容器id    (启动)
docker restart 容器id  (重启)
```

- 进入容器的方式

```
docker attach 容器id          (从这个容器退出，会导致容器停止)
docker exec -it 容器id bash   (从这个容器退出，不会导致容器停止)
```

- 保存镜像

```
docker save 镜像名  -o  /home/wzh/docker/文件名.tar
```

- 加载镜像

```
docker load -i 镜像的文件.tar  
```

- 导出容器

```
docker export 容器id > ubuntu.tar
```

- 导入容器

```
cat ubuntu.tar | sudo docker import - ubuntu:v2.0(本地路径)
docker import http://example.com/exampleimage.tgz example/imagerepo(远程url)
```

- 删除容器

```
docker rm 容器id	       (删除指定id的容器)
docker container prune  (清理掉所有处于终止状态的容器)
```

- 获取容器映射端口


```
docker port 容器id/容器名
```

- win10挂载文件夹到docker容器

```
docker run -itd -v "/g:/workSpace/docker/RDBus-0110":/home/RDBUS-0110 --name test 镜像名
```

- win10挂载文件到docker容器

```
docker run -itd -v "/g:/workSpace/docker/RDBus-0110/test.txt":/home/test.txt --name test 镜像名
```

### 3. 构建镜像

- 更新镜像

```
1. 运行容器   winpty docker run -it --name test1 ubuntu bash 
2. 修改容器内容， 如  apt-get update
3. 退出容器   exit
4. 提交容器   docker commit -m='has update' 容器id  目标镜像名:tag版本
```

- 使用Dockerfile创建新镜像

```
docker build -t 镜像名:tag名 . -f jdkDockerfile  (默认tag为latest)
```

- 重命名

```
docker tag 镜像id 新镜像名:tag名     (修改镜像名，复制一个新的)
docker rename 原容器id/名 新容器名    (容器改名，先改name再改id)
```

### 4. DockerFile指令

| 指令        | 说明                                                         | 例子                                        |
| ----------- | ------------------------------------------------------------ | ------------------------------------------- |
| FROM        | 指定基础镜像，特殊的空镜像scratch                            | FROM debian:jessie                          |
| RUN         | 执行命令行命令(构建镜像),接受shell和exec                     | RUN apt-get update /                        |
| MAINTAINER  | 维护者信息                                                   | MAINTAINER yq@qq.com                        |
| COPY        | 复制文件，COPY <源路径>... <目标路径>                        | COPY package.json /usr/src/app/             |
| ADD         | 复制并解压文件(可用url)，格式同COPY                          | ADD ubuntu-amd64-root.tar.gz /              |
| CMD         | 容器启动命令,接受shell和exec                                 | CMD echo $HOME                              |
| ENTRYPOINT  | 指令当成参数执行，后面可追加参数                             | ENTRYPOINT [ "curl", "-s", "http://ip.cn" ] |
| ENV         | 设置环境变量，供后面指令使用                                 | ENV NODE_VERSION 7.2.0                      |
| ARG         | 构建参数，容器运行时是不会存在                               | ARG <参数名>[=<默认值>]                     |
| VOLUME      | 保存动态数据到卷，不保存到存储层                             | VOLUME /data                                |
| EXPOSE      | 声明打算使用的端口，并不真正映射                             | EXPOSE <端口1> [<端口2>...]                 |
| WORKDIR     | 指定工作目录,以后各层的当前目录就被改为指定的目录,目录不存在自动创建 | WORKDIR <工作目录路径>                      |
| USER        | 指定当前用户，切换后面各层用户，不自动创建                   | USER redis                                  |
| HEALTHCHECK | 检查容器健康状况                                             | HEALTHCHECK --interval=5s --timeout=3s      |
| ONBUILD     | 帮助别人定制自己                                             | ONBUILD <其它指令>                          |

### 5. 摸坑路

1. 把springBoot打的jar放入ubuntu中，并加入jdk

   ```
   1.jdkDockerFile文件
   FROM ubuntu
   MAINTAINER 1486866853@qq.com
   
   COPY demo2-0.0.1-SNAPSHOT.jar /home/
   ADD jdk-8u221-linux-x64.tar.gz /usr/local/lib
   
   ENV JAVA_HOME /usr/local/lib/jdk1.8.0_221
   ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
   ENV PATH $PATH:$JAVA_HOME/bin
   
   CMD java -jar /home/demo2-0.0.1-SNAPSHOT.jar
   
   2.构建镜像
   docker build -t testjdk:v1 . -f jdkDockerFile
   
   3.启动镜像，创建容器
   docker run -d -p 8083:8083 --name testWeb 镜像id
   
   4.宿主机的访问
   http://192.168.46.143:8083/demo2/restTemplate/restTemplateGet1
   ```

   

### 6. 应用

#### 6.1 mysql

##### 6.1.1 单机

- 拉取镜像

  ```
  docker search mysql
  docker pull mysql
  ```

- 创建mysql数据存放文件夹

  ```
  mkdir /opt/docker_mysql
  chmod -R 777 /opt/docker_mysql
  cd /opt/docker_mysql
  ```

- 启动镜像

  ```
  docker run --name mysqlserver -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d -i -p 3306:3306 mysql:latest
  ```

- 进入mysql容器并开启远程访问权限

  ```
  docker exec -it mysqlserver bash
  
  mysql -uroot -p
  
  use mysql;
  
  select host,user,plugin from user;
  # 镜像里面 root用户已经有远程连接权限在里面，所以不需要去设置，只是模式不一样才导致无法连接，把root用户的密码改成 caching_sha2_password(8.0后新模式) mysql_native_password(8.0前模式) 模式，即可远程连接
  #ALTER USER 'root'@'%' IDENTIFIED WITH caching_sha2_password BY '123456';
  
  #flush privileges;
  ```

##### 6.1.2 PXC集群

```
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=123456 -v v1:/var/lib/mysql --privileged --name=node1 --network=net1 --ip 172.20.0.2 percona/percona-xtradb-cluster
docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=123456 -e --privileged -v v2:/var/lib/mysql --name=node2 --net=net1 --ip 172.20.0.3 percona/percona-xtradb-cluster
docker run -d -p 3308:3306 -e MYSQL_ROOT_PASSWORD=123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=123456 -e --privileged -v v3:/var/lib/mysql --name=node3 --net=net1 --ip 172.20.0.4 percona/percona-xtradb-cluster
docker run -d -p 3309:3306 -e MYSQL_ROOT_PASSWORD=123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=123456 -e --privileged -v v4:/var/lib/mysql --name=node4 --net=net1 --ip 172.20.0.5 percona/percona-xtradb-cluster
docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=123456 -e --privileged -v v5:/var/lib/mysql --name=node5 --net=net1 --ip 172.20.0.6 percona/percona-xtradb-cluster
```



### * 坑

- the input device is not a TTY.  If you are using mintty, try prefixing the command with 'winpty'

  原因：window10下没有用root用户登录

  解决方法：①需要在指令前加winpty；②用管理员登录的cmd运行指令

- dockerFile的COPY和ADD只能复制当前目录下文件，不能复制其他目录下文件

- 登录harbor需要在 settings - daemon - insecure registries 中加入 https://172.16.1.99/ 