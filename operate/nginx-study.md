<h1><center>nginx-study</center></h1>
### 1. nginx简介

1.    动静分离 

   ```
   1. Nginx是一种轻量级，高性能，多进程的Web服务器，非常适合作为静态资源的服务器使用，而动态的访问操作可以使用稳定的Apache、Tomcat及IIS等来实现； 
   2. 静态资源通过绝对路径去访问，放在nginx服务器当中。
   ```

2.    负载均衡 

   ```
   1. 分配方式： 轮询、权重、iphash、最少连接、fair
   2. Session问题： 当我们确定一系列负载的服务器后，那我们的WEB站点会分布到这些服务器上。这个时候如果采用Test2 每一次请求随机访问任何一台服务器上，这样导致你访问A服务器后，下一次请求又突然转到B服务器上。这个时候与A服务器建立的Session，传到B站点服务器肯定是无法正常响应的。
      三种解决方式：①Session或凭据缓存到独立的服务器；②Session或凭据保存数据库中；③nginx ip_hash 保持同一IP的请求都是指定到固定的一台服务器
   3. 文件上传下载： 如果实现了负载均衡，除了Session问题，我们还会碰到文件的上传下载问题。文件不可能上传不同的服务器上，这样会导致下载不到对应文件的问题。
      两种解决方式： ①独立文件服务器 ②文件压缩数据库
   ```

3.    反向代理 

   ```
   1. 正向代理：一个位于客户端和原始服务器之间的服务器，为了从原始服务器取的内容，客户端向代理发送一个请求并指定目标（原始服务器），然后代理向原始服务器转交请求并将获得的内容返回给客户端；
   2. 反向代理服务器：在服务器端接收客户端的请求，然后把请求分发给具体的服务器进行处理，然后再将服务器的响应结果反馈给客户端；
   3. 区别： 正向代理即是客户端代理, 代理客户端, 服务端不知道实际发起请求的客户端；
            反向代理即是服务端代理, 代理服务端, 客户端不知道实际提供服务的服务端
   ```

4.    单点故障 

   ```
   某台节点服务器挂了，但是Nginx仍然会可能选中这个出故障的机器，然后就一直连接着是因为超时时间很长，具体多长不清楚，所以为了避免一直连接着，我们需要设置超时时间;
   用Keepalived搭建双Nginx server集群，防止单点故障
   ```

### 2. 安装

#### 2.1 linux(以centos7为例)

1.  安装编译依赖环境

   1) gcc gcc-c++
   2) pcre 和 pcre-devel
   3) openssl 和 openssl-devel
   4) zlib 和 zlib-devel

   ```
   1. 查看是否安装
   rpm -qa | grep gcc 
   rpm -ql gcc
   yum list installed | grep gcc
   2. 安装命令
   在线： yum install -y gcc
   离线： rpm -ivh gcc      (提前下载离线安装包)
   3. 在线安装，例
   yum install -y gcc gcc-c++
   yum install -y pcre pcre-devel
   yum install -y openssl openssl-devel
   yum install -y zlib zlib-devel
   ```

2.  下载

   ```
   官网地址: http://nginx.org/download
   获取安装包：  wget http://nginx.org/download/nginx-1.18.0.tar.gz  
   ```

3.  安装

   ```
   解压：           tar -zxvf nginx-1.18.0.tar.gz -C /opt
   切换到管理员		sudo - root
   切换到解压目录     cd /opt/nginx
   检测平台安装条件	./configure --prefix=/usr/local/nginx (默认安装目录为/usr/local/nginx)
   编译安装		  make && make install			
   ```

4.  操作

   ```
   启动： /usr/local/nginx/sbin/nginx 
   停止:  /usr/local/nginx/sbin/nginx -s stop	
   重载:  /usr/local/nginx/sbin/nginx -s reload
   查看:  ps aux | grep nginx
   访问:	 浏览器访问localhost:80（默认端口80）
   ```

5.  复制nginx

   ```
   当前测试发现， 不同服务器的相同路径可以直接复制使用， 同一个服务器还不知道
   ```

#### 2.2 win10

1. 下载

   ```
   官网地址： http://nginx.org/en/download.html（nginx/Windows-1.18.0）
   ```

2. 安装及操作

   ```
   安装：  将.zip解压后即可直接使用
   启动：  nginx目录下， 双击 nginx.exe 或 命令行输入 start nginx
   查看：	 tasklist | findstr nginx.exe
   关闭：  管理员启动命令行，输入  taskkill -f -t /im nginx.exe
   访问:	 浏览器访问localhost:80（默认端口80）
   ```

### 3. 配置

1.  简单配置

   ```
   listen       8090;
   server_name  localhost;
   
   #charset koi8-r;
   
   #access_log  logs/host.access.log  main;
   
   location /SR/ {
   	rewrite  ^.+SR/?(.*)$ /$1 break;
   	add_header backendIP $upstream_addr;
   	add_header backendCode $upstream_status;
   	proxy_pass    http://172.18.9.33:8083;    					# 设置代理
   }
   		
   location /CONNECT/ {
   	rewrite  ^.+CONNECT/?(.*)$ /$1 break;
   	add_header backendIP $upstream_addr;
   	add_header backendCode $upstream_status;
   	proxy_pass    http://172.18.9.33:8084;    					# 设置代理
   }
   
   location / {
   	root   C:/Users/yq/Desktop/webConsole;   	# 指向目标文件位置（相对和绝对路径都可）
   	index  index.html;
   	try_files $uri $uri/ @router;				# 解决nginx中vue项目刷新报404问题
   	index index.html;
   }
   		
   location @router {
   	rewrite ^.*$ /index.html last;
   }
   ```

2.  坑

   ```
   1. location中路径和 root中路径合起来即项目真正路径， root + location = 实际路径
   2. 有router的vue程序需要对路由设置，否则刷新页面就没了
   3. 删除nginx下的logs文件夹，nginx服务启动不了
   4. 错误信息   123: The filename, directory name, or volume label syntax is incorrect
      解决方法	  配置文件的root路径中用/，改为\
   5. 错误信息   1113: No mapping for the Unicode character exists in the target multi-byte code page
      解决方法   nginx放在含有中文字符的目录下，把中文路径去掉即可
   ```

### 4. 负载均衡

1.  启动三个服务（以nginx为例）

   ```
   server1:8085     /opt/nginx/server1/sbin/nginx
   server2:8086     /opt/nginx/server2/sbin/nginx
   server3:8087     /opt/nginx/server3/sbin/nginx
   ```

2.  配置负载均衡的nginx（轮询）

   ```
    
   # 更改Nginx服务的默认用户，增加安全性
   user  nginx;
   worker_processes  1;
   
   events {
       worker_connections  1024;
   }
   
   http {
      upstream gs-server {
          server 192.168.72.133:8085;
          server 192.168.72.133:8086;
          server 192.168.72.133:8087;
      }
   
       server {
           listen       8081;
           server_name  localhost;
   
           location / {
              proxy_pass http://gs-server;
              proxy_redirect default;
              # root   html;
              # index  index.html index.htm;
           }
   
           error_page   500 502 503 504  /50x.html;
           location = /50x.html {
               root   html;
           }
       }
   }         
   ```

3.  创建默认的nginx用户

   ```
   查看当前已存在用户：     cat /etc/passwd | grep -v /sbin/nologin | cut -d : -f 1
   新增用户：   		  useradd nginx
   ```

4. 启动

   ```
   /opt/nginx/nginx-test1/sbin/nginx
   ```

5. 访问

   ```
   192.168.72.133:8081
   ```

   

