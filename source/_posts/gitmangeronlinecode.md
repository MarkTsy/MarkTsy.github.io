---
title: Linux Git仓库管理线上代码
date: 2020-03-12 15:40:55
tags: [项目部署,git远程仓库]
categories: Git
---

### 前言

本篇blog主要说明的是如何将本地的项目代码通过git直接push到线上发布，未作更深的权限管理及linux的用户管理问题的考量。

<!--more-->

#### 服务端

##### 安装Git

```bash
$ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
$ yum install git
```

##### 初始化Git仓库

以root用户及目录为例

```bash
$ cd /root
$ mkdir gittest
$ cd gittest
$ git init --bare test.git
Initialized empty Git repository in /home/gitrepo/test.git/
```

##### git仓库与项目源码分离

```bash
$ cd test.git/hooks
$ vi post-receive
```

输入

> git --work-tree=/root/gittest --git-dir=/root/gittest/test.git checkout -f

/root/gittest  #代码目录
/root/gittest/test.git   #git库目录

#### 本地

```bash
    git clone root@ServerName:/root/gittest/test.git
    git push origin master
```

即可在服务端/root/gittest目录下查看到提交的文件, 把/root/gittest作为web目录, 即可.
