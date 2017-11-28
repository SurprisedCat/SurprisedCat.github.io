---
layout: post
title:  "Linux上SVN服务器搭建"
categories: Tools 
tags: Linux Tools
author: SurprisedCat
---

* content
{:toc}

一般我们使用git进行代码的版本管理，但是git自身只能识别文本文件。我们常用的office系列是文本与二进制的组合，git diff等操作无法正常使用。况且office软件自带diff的功能，我仅仅想做一个版本管理与云同步，在不想用SharePoint的倾向下，我选择在云主机上搭建一个基于Debian Linux的SVN服务器。

<!--excerpt_separator_here-->

## Linux下的svn服务器 ##

Subversion是一个自由开源的版本控制系统。在Subversion管理下，文件和目录可以超越时空。Subversion将文件存放在中心版本库里，这个版本库很像一个普通的文件服务器，不同的是，它可以记录每一次文件和目录的修改情况，这样就可以借此将数据恢复到以前的版本，并可以查看数据的更改细节。$^{[1]}$

Subversion集服务端和客户端一体，当linux中拥有Subversion之后，既可以做SVN的服务端，也可以作为客户端。而在Windows下，服务端与客户端通常是不同的软件，例如服务端使用Visualsvn server,客户端使用TortoiseSVN。

在Debian Linux下安装Subversion特别简单``sudo apt install subversion``即可。

## 创建工作目录与库 ## 

首先我们需要给Subversion创建一个目录作为工作区。

```shell
mkdir ~/svn
```

工作目录是存放SVN库的目录，因为会创建不只一个的库，比如下面的场景，接下来到工作目录中创建svn版本库。

```shell
cd svn
svnadmin create repo1
svnadmin create repo2
```

这样就在工作目录下创建了两个版本库。

## 配置一个库 ##

SVN版本库的配置文件都放在对应版本库的``conf``目录下，我们需要用到**authz、passwd、svnserve.conf**这三个文件。

首先编辑svnserve.conf主配置文件(应仔细阅读该文件里面的注释，了解每一项的作用)，对以下几项修改如下:$^{[2]}$

```shell
[general]

anon-access = none    #取消匿名访问

auth-access = write    #授权用户有可写权限

password-db = passwd    #指定用户配置文件，后面会用到

authz-db = authz    #指定权限配置文件，后面会用到
```

编辑passwd文件，建立svn客户端用户以及密码，一行一个，这里建立了两个用户,对应我的两台主机。

```shell
### This file is an example password file for svnserve.
### Its format is similar to that of svnserve.conf. As shown in the
### example below it contains one section labelled [users].
### The name and password for each user follow, one account per line.

[users]
# harry = harryssecret
# sally = sallyssecret
cx-laptop = buptlab635
cx-server = buptlab635
```

编辑authz文件，指定SVN仓库目录，以及每个认证用户的权限，这里均为可读可写

```shell
[aliases]
# joe = /C=XZ/ST=Dessert/L=Snake City/O=Snake Oil, Ltd./OU=Research Institute/CN=Joe Average

[groups]
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe

# [/foo/bar]
# harry = rw
# &joe = r
# * =

# [repository:/baz/fuz]
# @harry_and_sally = rw
# * = r

[repo1:/]
cx-laptop = rw
cx-server = rw
```

启动SVN服务，-d表示后台运行svn服务，-r是指定svn目录；然后执行了netstat检查端口，默认监听在3690端口上。


```shell
svnserve -d -r ~/svn/
netstat -anp | grep 3690  # 通过查看这个端口判断SVN服务器是否正常启动
```

## windows客户端TortoiseSVN的操作 ##

在Windows使用SVN服务的话，需要下载一个TortoiseSVN客户端软件，安装好软件后在本地电脑创建一个工作目录，右键该目录选择checkout，checkout就是把SVN服务器上的文件下载到本地工作目录内的操作。

![tortoiseSVN操作](/assets/tortoisesvn1.JPG)

在对话框里输入SVN服务器的地址以及需要存放文件的目录。**repo1**是服务器工作区的一个版本库，然后点击ok按钮，之后会要求输入用户名密码，这个时候就输入在passwd里设置的信息即可。

![tortoiseSVN操作](/assets/tortoisesvn2.JPG)

## 路径问题 ##

许多人搭建SVN服务器不成功都是因为SVN版本库路径的问题。在搭建过程中，有几个路径需要注意：

1. SVN工作区所在路径，就是本文中``mkdir ~/svn``的路径，具体来说是``/home/your-host-name/svn/``，这个路径和``svnserve -d -r ~/svn/``的路径保持一致。
2. SVN版本库配置路径，即``authz``里面的路径，``[repo1:/]``这一项，这个名字要和对应版本库名字一致。``/``表示repo1这个版本库下的所有文件。
3. 客户端checkout路径，在客户端checkout的时候，``svn://server-ip``对应于服务器上``svnserve -d -r ~/svn/``的路径，因此在checkout的时候，需要再跟上版本库的名字，即加上的``repo1``。

## 参考文献 ##

[1] [https://baike.baidu.com/item/subversion/7818587?fr=aladdin](https://baike.baidu.com/item/subversion/7818587?fr=aladdin)

[2] [http://www.linuxidc.com/Linux/2016-04/130346.htm](http://www.linuxidc.com/Linux/2016-04/130346.htm)