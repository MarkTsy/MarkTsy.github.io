---
title: Centos 防火墙/端口 开关
date: 2020-03-10 19:11:40
tags: [防火墙，端口]
categories: Linux
---

![](centosfirewallportoc/u=3630922114,391211100&fm=26&gp=0.jpg)

<!--more-->

当我们在CentOS服务器中装了一些开发环境（如 tomcat、mysql、nginx 等...）时，希望能从外界访问，就需要配置防火墙对指定端口开放。

### CentOS 6.5

#### 1.开放指定端口

```bash
/sbin/iptables -I INPUT -p tcp --dport 端口号 -j ACCEPT //写入修改
/etc/init.d/iptables save //保存修改
service iptables restart //重启防火墙，修改生效
```

#### 2.关闭指定端口

```bash
/sbin/iptables -I INPUT -p tcp --dport 端口号 -j DROP //写入修改
/etc/init.d/iptables save //保存修改
service iptables restart //重启防火墙，修改生效
```

#### 3.查看端口状态

```bash
/etc/init.d/iptables status
```

### CentOS 7

#### 1.防火墙操作

启动： systemctl start firewalld
查看状态： systemctl status firewalld 
停止： systemctl disable firewalld
禁用： systemctl stop firewalld

#### 2.开放指定端口

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent //开放端口
firewall-cmd --reload //重新载入，使其生效
```

#### 3.关闭指定端口

```bash
firewall-cmd --zone=public --remove-port=80/tcp --permanent //关闭端口
firewall-cmd --reload //重新载入，使其生效
```

#### 4.查看端口状态

```bash
firewall-cmd --zone=public --query-port=80/tcp //查看端口状态
```
