---
title: Linux 安装 composer
date: 2020-03-10 21:40:45
tags: [composer]
categories: 教程
---

![](linuxinstallcomposer/u=2488564289,1129386811&fm=26&gp=0.jpg)

<!--more-->

#### 官方教程地址：

[https://docs.phpcomposer.com/00-intro.html#Globally](https://docs.phpcomposer.com/00-intro.html#Globally)

#### 局部安装

要真正获取 Composer，我们需要做两件事。首先安装 Composer （同样的，这意味着它将下载到你的项目中）：

```bash
curl -sS https://getcomposer.org/installer | php
```

注意： 如果上述方法由于某些原因失败了，你还可以通过 php >下载安装器：

```bash
php -r "readfile('https://getcomposer.org/installer');" | php
```

这将检查一些 PHP 的设置，然后下载 composer.phar 到你的工作目录中。这是 Composer 的二进制文件。这是一个 PHAR 包（PHP 的归档），这是 PHP 的归档格式可以帮助用户在命令行中执行一些操作。

你可以通过 --install-dir 选项指定 Composer 的安装目录（它可以是一个绝对或相对路径）：

```bash
curl -sS https://getcomposer.org/installer | php -- --install-dir=bin
```

#### 全局安装
你可以将此文件放在任何地方。如果你把它放在系统的 PATH 目录中，你就能在全局访问它。 在类Unix系统中，你甚至可以在使用时不加 php 前缀。

你可以执行这些命令让 composer 在你的系统中进行全局调用：

```bash
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

注意： 如果上诉命令因为权限执行失败， 请使用 sudo 再次尝试运行 mv 那行命令。
现在只需要运行 composer 命令就可以使用 Composer 而不需要输入 php composer.phar。
