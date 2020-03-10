---
title: Centos安装Apache
date: 2020-03-10 19:28:23
tags: [apache]
categories: 教程
---

### 安装apache

通过yum安装

```bash
yum install httpd
```

安装成功后可以执行启动命令：

```bash
service httpd start
```

<!--more-->

### 异常处理：

启动服务后无法访问

1）在防火墙中开放80端口 现在需要将 http 服务加入防火墙以允许外部访问，

```bash
firewall-cmd --add-service=http --permanent
```

–permanent 参数表示这是一条永久防火墙规则，如果不加则重启系统后就没有这条规则了

而对于自定义的端口（如81），也需要添加防火墙规则， firewall-cmd --zone=public --add-port=81/tcp --permanent 重启 Firewalld 使该规则生效， systemctl restart firewalld

2）关闭SELINUX

vi /etc/selinux/config

注释掉如下两句，添加最后一项

```
#SELINUX=enforcing  #注释掉

#SELINUXTYPE=targeted  #注释掉

SELINUX=disabled #增加

:wq!  保存退出
```
