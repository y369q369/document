<center><h1>nginx使用</h1></center>
### 1. 官网下载地址

​	https://nginx.org/download/（linux下载tar.gz）

​	http://nginx.org/en/download.html（windows下载nginx/Windows-1.17.8）



### 2. 使用

#### 2.1 win10操作

```
安装：  将.zip解压后即可直接使用
启动：  nginx目录下， 双击 nginx.exe 或 命令行输入 start nginx
查看：	 tasklist | findstr nginx.exe
关闭：  管理员启动命令行，输入  taskkill -f -t /im nginx.exe
访问:	 浏览器访问localhost:80（默认端口80）
```

#### 2.2 linux操作

```
依赖环境：	1) gcc gcc-c++
		   2) pcre 和 pcre-devel
		   3) openssl 和 openssl-devel
		   4) zlib 和 zlib-devel
	检查是否安装: 	rpm -qa gcc / rpm -qa | grep gcc(以此类推)
    安装环境：		 yum install gcc
安装nginx: 	1) 解压.tar.gz包 并 进入nginx的目录(管理员身份)
             2) ./configure (默认配置目录： /usr/local/nginx)
             3) make && make install
***     对nginx的操作需要进入nginx的sbin目录下操作	             
启动： ./nginx 
停止:  ./nginx -s stop		/			./nginx -s stop
重载:  ./nginx -s reload
查看:  ps aux | grep nginx
访问:	 浏览器访问localhost:80（默认端口80）
```



### 3. 配置内容

​	此处有坑点： ①location中路径和 root中路径合起来即项目真正路径， root + location = 实际路径

​    		②有router的vue程序需要对路由设置，否则刷新页面就没了

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

### 常见错误

```
删除nginx下的logs文件夹，nginx服务启动不了
```

```
配置文件的root路径中用/，改为\
123: The filename, directory name, or volume label syntax is incorrect
```

```
nginx放在含有中文字符的目录下，把中文路径去掉即可
1113: No mapping for the Unicode character exists in the target multi-byte code page
```

