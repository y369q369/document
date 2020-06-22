<h1><center>mysql-study</center></h1>
### 1. 新增用户

```
1. 创建 不限ip 的新用户gs
create user 'gs'@'%' identified by 'gs';
2. 给新用户gs赋予test数据库所有权限
grant all privileges  on test.* to "gs"@'%';
```

