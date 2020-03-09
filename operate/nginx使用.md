<center><h1>nginx使用</h1></center>
### 1. 官网下载地址

​	http://nginx.org/en/download.html（windows下载nginx/Windows-1.17.8）

### 2. 启动

​	解压压缩包后，双击 nginx.exe 或 命令行输入 start nginx

​	默认端口为80, 启动后可访问 localhost:80

​	查看nginx的端口命令  tasklist | findstr nginx.exe

### 3. 关闭

​	管理员启动命令行，输入  taskkill -f -t /im nginx.exe

### 4. 配置内容

​	此处有坑点： ①location中路径和 root中路径合起来即项目真正路径， root + location = 实际路径

​    		②有router的vue程序需要对路由设置，否则刷新页面就没了

```
listen       8090;
server_name  localhost;

#charset koi8-r;

#access_log  logs/host.access.log  main;

location / {
	root   E:/project/webstrom/rdbus-webconsole-web/dist;   	# 指向目标文件位置（相对和绝对路径都可）
	index  index.html;
	try_files $uri $uri/ @router;
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

