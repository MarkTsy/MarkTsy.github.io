---
title: Mysql 远程登录
date: 2020-03-10 19:25:17
tags: [mysql,远程登录]
categories: Mysql
---



```sql
mysql>  alter user 'root'@'localhost' identified with mysql_native_password by '123456';
Query OK, 0 rows affected (0.60 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.18 sec)
```

<!--more-->

记得关闭防火墙或者，取消3306端口的防火墙


