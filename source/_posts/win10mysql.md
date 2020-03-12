---
title: Win10 MySQL 下载/安装
date: 2020-03-08 15:41:46
tags: [MySQL安装,MySQL卸载]
categories: Mysql
---

## 下载

官网下载地址:
[https://www.mysql.com/](https://www.mysql.com/)

<!--more-->

![](win10-mysql/20200308154339.png)
![](win10-mysql/20200308154405.png)
![](win10-mysql/20200308154428.png)
![](win10-mysql/20200308154702.png)

## 安装

1.解压缩

2.配置bin目录到环境变量

3.my.ini 数据库默认配置文件

```bash
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=E:\Program Files\mysql-8.0.12-winx64
# 设置mysql数据库的数据的存放目录
datadir=E:\Program Files\mysql-8.0.12-winx64\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
# mysql_native_password
default_authentication_plugin=mysql_native_password
# 设置默认时区为东八区
default-time-zone = '+8:00'
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8`
```

4.管理员运行cmd

5.初始化数据库

```bash
mysqld --initialize-insecure //(root 空密码)
mysqld --initialize //(root 随机密码)
```

6.安装Mysql服务

```bash
mysqld install
```

7.运行Mysql

```bash
net start mysql
```

也可在系统服务中开启

## 常用命令

```sql
USE 数据库名;
```

选择要操作的Mysql数据库，使用该命令后所有Mysql命令都只针对该数据库。

```sql
SHOW DATABASES;
```

列出 MySQL 数据库管理系统的数据库列表。

```sql
SHOW TABLES;
```

显示指定数据库的所有表，使用该命令前需要使用 use 命令来选择要操作的数据库。

```sql
SHOW COLUMNS FROM 数据表;
```

显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。

```sql
SHOW INDEX FROM 数据表;
```

显示数据表的详细索引信息，包括PRIMARY KEY（主键）

```sql
SHOW TABLE STATUS [FROM db_name] / [LIKE 'pattern'];
```

该命令将输出Mysql数据库管理系统的性能及统计信息。

```sql
desc tablename;
```

查看表的结构

```sql
show create table tablename;
```

查看创建表的sql语句

## root密码重置

1.以管理员身份运行cmd 停止服务

```bash
net stop mysql
```

2.设置跳过验证，进入到mysql安装目录下的bin路径，在mysql/bin/目录下输入命令：

```bash
mysqld --shared-memory --skip-grant-tables
```

（注意：一定要有–shared-memory，否则无法正常设置–skip-grant-tables并启动mysql服务）
正常情况下，输完这条命令，该命令行窗口应该卡住不动。

3.无密码登录:新开一个CMD窗口,进入到mysql安装目录下的bin路径,无需重复启动mysql服务,在mysql/bin/目录下输入"mysql",此时应该可以连接成功,作者尝试的时候大多是卡在了这里,原因参照第二步.

4.重置root密码为空(注意：这里需先置为空密码，否则无法登陆):
在第三步的cmd窗口中输入命令：

```sql
update mysql.user set authentication_string='' where User = 'root';.
```

5.退出所有命令行，重新登陆数据库，（若显示服务未启动，需先启动MySQL服务，输入net start mysql）
使用如下命令重新修改root密码：

```sql
alter user 'root'@'localhost' identified by '123';
```
