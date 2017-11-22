---
layout: post
title:  "树莓派首次开机一根网线的控制（ssh与配置文件）"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

最近的设备比较多，搞了一个KVM切换器，让多个主机共享一套显示器和键鼠。但是明显树莓派这个东东不太需要单独给他再加个外设，树莓派上的程序基本不会用的GUI，都是shell能搞定的东西，所以ssh就能搞定树莓派上所有的问题。

## 开启ssh ##

Raspberry Pi 3自带openssh服务器，可以直接通过ssh连接操作树莓派，但是第一次开机的时候，ssh并未自动开启（之前的版本可以，3代不可以）。官网的解决方案很简单，在树莓派存储介质（SD卡或者U盘）上加一个名为``ssh``的空文件，就开启了ssh服务。

>注意：千万不要用windows的记事本新建这个文件！

excerpt_separator_here

## 获取Raspberry的IP ##

当ssh服务开启之后，我们还需要树莓派的IP才能登陆树莓派。

如果你的树莓派插在一个路由器或者交换机上，网络中自然会有DHCP服务器在树莓派分配IP，这是可以借助nmap、ipscan22.exe等扫描工具搜寻Raspberry的地址。树莓派的MAC地址为b8-27-eb-XX-XX-XX，有些能显示网络名称的工具直接会显示Raspberry Pi。

如果可以登陆路由器，那么树莓派的信息就会显示在路由器管理界面的设备管理页面。

如果树莓派直接连接电脑上，可以通过在电脑上安装一个DHCP服务器来给树莓派分配IP，例如isc-dhcp-server。

如果这些都觉得太复杂，可以采取修改cmdline.txt的方法。打开U盘中的cmdline.txt文件，在结尾加入语句ip=***.***.***.***（IP地址）。然后在把自己的网段配成和cmdline.txt里一样的网段，就可以连接树莓派了。树莓派的默认账户密码：pi -- raspberry

## config.txt ##

由于树莓派并没有传统意义上的BIOS, 所以现在各种系统配置参数通常被存在"config.txt"这个文本文件中。树莓派的config.txt文件会在ARM内核初始化之前被**GPU**读取。这个文件存在引导分区上的。对于Linux, 路径通常是/boot/config.txt, 如果是Windows (或者OS X) 它会被识别为SD卡中可访问部分的一个普通文件。

你可以使用下列命令去获取当前激活的设置: 

* 列出指定的配置参数. 
* 例如: vcgencmd get_config arm_freq 
    + vcgencmd get_config 
* 列出所有已设置的整形配置参数(非零) 
    + vcgencmd get_config int 
* 列出所有已设置的字符型配置参数(非零) 
    + vcgencmd get_config str

文件格式:

当值是整形时格式为”属性=值”。 每行只指定一个参数。 注释使用’#’井号作为一行开头。 
注意: 在新版的树莓派里每行都有#注释, 要想使用该行参数只需移除#。

具体的文件配置强烈推荐一下两篇文章：

[https://elinux.org/RPiconfig](https://elinux.org/RPiconfig)

[https://elinux.org/R-Pi_configuration_file](https://elinux.org/R-Pi_configuration_file)


## 更新pi、root的密码 ##


## 更新软件源 ##


## 参看文献 ##

