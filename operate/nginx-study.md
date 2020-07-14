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
   解压：           tar -zxvf /root/download/nginx-1.18.0.tar.gz -C /root/software
   切换到解压目录     cd /root/software/nginx-1.18.0
   检测平台安装条件	./configure --prefix=/opt/nginx/test (默认安装目录为/usr/local/nginx)
   编译安装		  make && make install			
   ```

4.  操作

   ```
   启动： /opt/nginx/test/sbin/nginx 
   停止:  /opt/nginx/test/sbin/nginx -s stop	
   重载:  /opt/nginx/test/sbin/nginx -s reload
   查看:  ps aux | grep nginx
   访问:	 浏览器访问localhost:80（默认端口80）
   ```

5.  复制nginx

   ```
   当前测试发现， 不同服务器的相同路径可以直接复制使用， 同一个服务器不行
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

1.  简单配置说明

   ```
   worker_processes  1;							# nginx的工作进程数（1个线程，多个进程）
   events {
       worker_connections  1024;					# nginx的连接数
   }
   http {
       include       mime.types;
       default_type  application/octet-stream;
   
       sendfile        on;
   
       keepalive_timeout  65;
   
       server {
           listen       80;						# nginx程序的访问端口
           
           location /CONNECT/ {						
               rewrite  ^/CONNECT/?(.*)$ /$1 break;		# 重写路径: 去掉/CONNECT
               add_header backendIP $upstream_addr;		# 返回头部添加 后台程序ip
               add_header backendCode $upstream_status;    # 返回头部添加 HTTP状态码
               proxy_pass    http://172.18.9.33:8084;    	# 设置代理的ip及端口
           }
   
           location / {
               root   html;						# 静态html代码位置(默认nginx目录下html)
               try_files $uri $uri/ @router;		# 解决nginx中vue项目刷新报404问题
               index  index.html index.htm;
           }
           
           location @router {						# 解决nginx中vue项目刷新报404问题
               rewrite ^.*$ /index.html last;
           }
   
           error_page   500 502 503 504  /50x.html;		# 设置错误页面
           location = /50x.html {
               root   html;
           }
   	}
   }	
   ```

   

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

### 4. 单个nginx

1.  安装nginx的服务server1， 即重新编译到/opt/nginx/server1

   ```
   1. /root/software/nginx-1.18.0/configure --prefix=/opt/nginx/server1 
   2. make && make install
   ```

2.  修改配置文件(port:8085):       vim /opt/nginx/server1/conf/nginx.conf

   ```
   worker_processes  1;							
   events {
       worker_connections  1024;					
   }
   http {
       include       mime.types;
       default_type  application/octet-stream;
   
       sendfile        on;
   
       keepalive_timeout  65;
   
       server {
           listen       8085;						# 访问端口 8085
   
           location / {
               root   html;						
               index  index.html index.htm;
           }
   
           error_page   500 502 503 504  /50x.html;
           location = /50x.html {
               root   html;
           }
   	}
   }	
   ```

3.  修改 html页面 的内容：  vim /opt/nginx/server1/html/index.html

   ```
   <!DOCTYPE html>
   <html>
   <head>
   <title>Welcome to nginx!</title>
   <style>
       body {
           width: 35em;
           margin: 0 auto;
           font-family: Tahoma, Verdana, Arial, sans-serif;
       }
   </style>
   </head>
   <body>
   <h1> this is server1 </h1>
   </body>
   </html>
   ```

4. 启动服务

   ```
   1.启动    /opt/nginx/server1/sbin/nignx
   2.访问    http://192.168.72.133:8085
   ```

   

### 5. 负载均衡

1.  安装四个nginx服务（三个用作web访问的nginx， 一个作为负载均衡的nginx）

   | 作用域                    | ip和port            | 安装位置           |
   | ------------------------- | ------------------- | ------------------ |
   | web服务1    =>    server1 | 192.168.72.133:8085 | /opt/nginx/server1 |
   | web服务2    =>    server2 | 192.168.72.133:8086 | /opt/nginx/server2 |
   | web服务3    =>    server3 | 192.168.72.133:8087 | /opt/nginx/server3 |
   | 负载均衡      =>    LB1   | 192.168.72.133:8081 | /opt/nginx/LB1     |

2. 启动三个web服务

   ```
   1.修改端口及html
      server1 ：   端口 - 8085;   html - this is server1
      server2 ：   端口 - 8086;   html - this is server2
      server3 ：   端口 - 8087;   html - this is server3
   
   2.启动
   server1:8085     /opt/nginx/server1/sbin/nginx
   server2:8086     /opt/nginx/server2/sbin/nginx
   server3:8087     /opt/nginx/server3/sbin/nginx
   ```

2.  配置负载均衡的nginx（轮询）

   ```
   user  nginx;										# 更改Nginx服务的默认用户，增加安全性
   worker_processes  1;
   
   events {
       worker_connections  1024;
   }
   
   http {
      upstream gs-server {								# 设置轮询访问的ip及端口
          server 192.168.72.133:8085;
          server 192.168.72.133:8086;
          server 192.168.72.133:8087;
      }
   
       server {
           listen       8081;							# 设置端口为8081
           server_name  localhost;
   
           location / {
              proxy_pass http://gs-server;				# 设置代理服务，与上方的 upstream 对应
              add_header backendIP $upstream_addr;
              add_header backendCode $upstream_status;
              proxy_redirect default;
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
   /opt/nginx/LB1/sbin/nginx
   ```

5. 访问（刷新页面可发现，显示内容变化，证明负载均衡完成）

   ```
   192.168.72.133:8081
   ```

   

### 6. nginx 与 keepalived 高可用

1.  安装五个nginx服务（三个用作web访问的nginx， 两个作为负载均衡的nginx）

   | 作用域                    | ip和port            | 安装位置           |
   | ------------------------- | ------------------- | ------------------ |
   | web服务1    =>    server1 | 192.168.72.133:8085 | /opt/nginx/server1 |
   | web服务2    =>    server2 | 192.168.72.133:8086 | /opt/nginx/server2 |
   | web服务3    =>    server3 | 192.168.72.133:8087 | /opt/nginx/server3 |
   | 负载均衡1    =>    LB1    | 192.168.72.133:8081 | /opt/nginx/LB1     |
   | 负载均衡2    =>    LB2    | 192.168.72.133:8081 | /opt/nginx/LB2     |

2.  启动nginx服务

   ```
   1.修改端口及html
      	server1 ：       端口 - 8085;   html - this is server1
      	server2 ：       端口 - 8086;   html - this is server2
      	server3 ：       端口 - 8087;   html - this is server3
     	nginx-test1 ：   端口 - 8081;  
      	nginx-test2 ：   端口 - 8082
   
   2.启动
   	server1     	/opt/nginx/server1/sbin/nginx
   	server2     	/opt/nginx/server2/sbin/nginx
   	server3     	/opt/nginx/server3/sbin/nginx
   	LB1    			/opt/nginx/LB1/sbin/nginx
   	LB2    			/opt/nginx/LB2/sbin/nginx
   ```

3. 安装keepalived

   ```
   1. 安装    		 yum install -y keepalived
   
   2. 修改配置     	vim /etc/keepalived/keepalived.conf
   global_defs {
      notification_email {						# 报错时邮箱接收者
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
      }
      notification_email_from Alexandre.Cassen@firewall.loc     # 报错时邮箱发送者
      smtp_server 192.168.200.1				# 邮件服务器地址
      smtp_connect_timeout 30					# 连接超时
      router_id 192.168.72.150                 # ip addr 查看的ip, 也可用hostName   
      vrrp_skip_check_adv_addr
      vrrp_strict
      vrrp_garp_interval 0
      vrrp_gna_interval 0
   }
   
   vrrp_instance VI_1 {
       state BACKUP					# 服务器状态 MASTER是主服务器
       interface ens33					# 通信端口 通过ip addr查看
       virtual_router_id 51			# vrrp实例id keepalived集群，实例id必须一致
       priority 100					# 权重比 主服务器的priority要比备份服务器大
       advert_int 1					# 心跳间隔(s) 通过心跳检测，如果发送心跳没反应 就立刻接管
       authentication {				# 服务器之间通信密码
           auth_type PASS
           auth_pass 1111
       }
       virtual_ipaddress {				# 自定义虚拟IP,
           192.168.72.180
       }
   }
   
   
   3. 常用命令
   启动     systemctl start keepalived.service		 /		 service keepalived start
   停止     systemctl stop keepalived.service      	 /		 service keepalived stop
   重启     systemctl restart keepalived.service    	 /		 service keepalived restart
   查看状态  systemctl status keepalived.service		/		servuce keepalived status
   ```

4.  配置 LB1 和 LB2 配置文件（给出不同部分）

   | 配置                   | LB1            | LB2            |
   | ---------------------- | -------------- | -------------- |
   | global_defs.router_id  | 192.168.72.133 | 192.168.72.150 |
   | vrrp_instance.state    | MASTER         | BACKUP         |
   | vrrp_instance.priority | 150            | 100            |

5.  启动keepalived

   ```
   LB1    					service keepalived start
   LB2    					service keepalived start
   ```

6.  访问

   ```
   192.168.72.180:8081
   ```

   