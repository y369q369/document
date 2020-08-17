<h1><center>spring-cloud学习</center></h1>

## 1. SpringBoot 回顾

### 1.1 启动

```
mvn:  mvn spring-boot:run
jar:  java -jar xxx.jar
```

### 1.2 命令行参数

```
指定 application.yml 中参数 
     格式： java -jar xxx.jar  --参数全称=参数值
     例子： java -jar xxx.jar  --server.port=8888 --spring.profiles.active=prod
```

