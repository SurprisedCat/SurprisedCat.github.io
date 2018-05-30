---
layout: post
title:  "树莓派安装 docker"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

## 系统选择 ##

我使用的是2018-04-18的RASPBIAN STRETCH LITE版本，我之前用过时间靠前的版本但是安装总是失败，还了这个版本是成功的。
官网上说:
> To install Docker CE, you need the 64-bit version of one of these Debian or Raspbian versions:  
* Buster 10 (Docker CE 17.11 Edge only)  
* Stretch 9 (stable) / Raspbian Stretch  
* Jessie 8 (LTS) / Raspbian Jessie  
* Wheezy 7.7 (LTS)   
> Docker CE is supported on both x86_64 (or amd64) and armhf architectures for Jessie and Stretch.  

但是我看了树莓派目前的官方版本是32位的。  

```shell
file /bin/dash
/bin/dash: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 3.2.0, BuildID[sha1]=18bddc2d67c785bbaaf97cbb2c0dd5420a1861b3, stripped
```

但是，**也安装成功了。**

## 修改树莓派源 ##

我认为这是非常重要的一步，我在用官方源的时候opencas的镜像总是无法访问，后来换成了

```shell
# /etc/apt/sources.list
deb http://mirrors.shu.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```

之后，奇迹的可以用了。

<!--excerpt_separator_here-->

## 安装docker ##

根据官方文档的说法：一些方式是不适用于rasbian系统的。
> Raspbian users cannot use this method!  
For Raspbian, installing using the repository is not yet supported. You must instead use the **convenience script**.

官方推荐是使用一个**便捷脚本**，我在没有改源之前使用这个脚本也没有成功，换了源就安装成功了。

```shell
pi@raspberrypi:~ $ curl -fsSL get.docker.com -o get-docker.sh
pi@raspberrypi:~ $ sudo sh get-docker.sh 
# Executing docker install script, commit: 36b78b2
+ sh -c apt-get update -qq >/dev/null
+ sh -c apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
+ sh -c curl -fsSL "https://download.docker.com/linux/raspbian/gpg" | apt-key add -qq - >/dev/null
Warning: apt-key output should not be parsed (stdout is not a terminal)
+ sh -c echo "deb [arch=armhf] https://download.docker.com/linux/raspbian stretch edge" > /etc/apt/sources.list.d/docker.list
+ [ raspbian = debian ]
+ sh -c apt-get update -qq >/dev/null
+ sh -c apt-get install -y -qq --no-install-recommends docker-ce >/dev/null
+ sh -c docker version
Client:
 Version:      18.05.0-ce
 API version:  1.37
 Go version:   go1.9.5
 Git commit:   f150324
 Built:        Wed May  9 22:24:36 2018
 OS/Arch:      linux/arm
 Experimental: false
 Orchestrator: swarm

Server:
 Engine:
  Version:      18.05.0-ce
  API version:  1.37 (minimum version 1.12)
  Go version:   go1.9.5
  Git commit:   f150324
  Built:        Wed May  9 22:20:37 2018
  OS/Arch:      linux/arm
  Experimental: false
If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

  sudo usermod -aG docker your-user

Remember that you will have to log out and back in for this to take effect!

WARNING: Adding a user to the "docker" group will grant the ability to run
         containers which can be used to obtain root privileges on the
         docker host.
         Refer to https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
         for more information.
pi@raspberrypi:~ $ sudo usermod -aG docker `whoami`
```

重启下cmd界面（否则还是需要sudo），安装成功。

## 测试 ##

```shell 
pi@raspberrypi:~ $ docker run -ti armhf/alpine:3.5 /bin/sh 
Unable to find image 'armhf/alpine:3.5' locally
3.5: Pulling from armhf/alpine
e68f2aaec91c: Pull complete 
Digest: sha256:5959291b202522ad454cab5ee9960d5a7bc2c59a88ddb00a5de01d36fb70ca9e
Status: Downloaded newer image for armhf/alpine:3.5

/ # echo "Hi, this is a tiny Linux distribution!" | base64 
SGksIHRoaXMgaXMgYSB0aW55IExpbnV4IGRpc3RyaWJ1dGlvbiEK
/ # cat /etc/issue
Welcome to Alpine Linux 3.5
Kernel \r on an \m (\l)

/ # exit
```

## 参考文献 ## 

[Docker官网](https://docs.docker.com/install/linux/docker-ce/debian/#install-using-the-repository)