---
title: Centos 安装PHP7
date: 2020-03-10 19:32:26
tags: [php安装]
categories: PHP
---

![](centosinstallphp7/u=426857830,3900731748&fm=26&gp=0.jpg)

<!--more-->

一、PHP的安装
　1、由于linux的yum源不存在php7.x，所以我们要更改yum源：

```bash
#rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
#rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

　2、yum查询安装php71w

```bash
#yum search php71w　
```

　3、yum 安装php71w和各种拓展，选自己需要的即可。

```bash
#yum install php71w php71w-cli php71w-common php71w-devel php71w-embedded php71w-fpm php71w-gd php71w-mbstring php71w-mysqlnd php71w-opcache php71w-pdo php71w-xml
```

　4、安装完成之后，#whereis php 可以看到php的安装目录，然后我们来给php配置环境,实际上在centos7上php.ini已经不用像在Windows上那样配置了。我们只需要修改这个文件的参数设置cgi.fix_pathinfo，默认设置值为“1”，用；进行注释或修改为0即可。

```bash
#vim vim /etc/profile #在末尾添加
PATH=$PATH:/usr/local/php/bin　　#记得修改为自己php的安装路径
export PATH
#：wq保存退出
source /etc/profile #运行该句指令使修改生效
php -v #就可以看到PHP版本信息了。
```

　5、测试，在自己的Nginx或Apache或自建的网页目录下新建index.php，编辑内容为<?php  phpinfo(); ?>
