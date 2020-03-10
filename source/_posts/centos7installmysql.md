---
title: Centos7 yum安装Mysql
date: 2020-03-10 14:45:10
tags: [Centos7,Mysql]
categories: 教程
---

![](centos7installmysql/u=3853313707,2686910450&fm=26&gp=0.jpg)

<!--more-->

### 官方教程：

[https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)

### Mysql官方程序包地址：

[https://repo.mysql.com/yum/](https://repo.mysql.com/yum/)

### 下载rpm程序包

(不同的版本可以去上面的程序包的库里找)

```bash
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
```

### 安装程序包

```bash
 rpm -ivh mysql-community-release-el7-5.noarch.rpm
```

### 安装mysql

```bash
yum -y install mysql mysql-server mysql-devel
```

### 启动服务

```
service mysqld start
```

### 连接数据库

```bash
mysql -u root -p  #回车 密码为空  直接回车登录
```

### 初始化数据库root密码

```bash
#选择数据库
use mysql 

#修改root用户密码：

#5.7版本之前
update user set password=password('root') where user='root';

#5.7及以后版本
update user set authentication_string=password('root') where user='root';

#刷新权限
flush privileges;

#退出
quit;
```

至此安装完成！
