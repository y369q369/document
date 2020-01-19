### 网路

```
查看 ：    ipconfig
```

### 路由

```
查看：    route print
新增：    route add 172.17.0.0/16 mask 255.255.255.0 10.0.75.2 -p
删除：    route delete 172.17.0.2
```

### 端口

```
查找端口:   netstat -ano | findstr "8080"
查找进程:   tasklist | find "25124"
杀进程:     taskkill -f -t /im xxx.exe
           taskkill /pid 25124 -t -f
```

