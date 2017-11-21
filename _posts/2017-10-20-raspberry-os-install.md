---
layout: post
title:  "树莓派系统安装与U盘启动"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

## 系统推荐 ##

Raspbian是默认的最常用于Raspberry Pi的免费操作系统。Raspbian是基于Debian的一个版本（针对Raspberry Pi硬件Debian进行了专门的优化，并构建了超过35000个Raspbian包）。Debian的软件策略偏保守，稳定第一，升级是次要的。

Raspbian也是树莓派官方支持的操作系统。这个系统镜像可以分为带GUI和不带GUI两种：

>Raspbain Stretch With PIXEL 树莓派官方系统，带PIXEL图形界面。特点是兼容性和性能优秀。

>Raspbain Stretch Lite 树莓派官方系统，不带图形界面。特点是兼容性和性能优秀，比PIXEL版本的安装包更小。

Raspbian仍处于积极开发状态，并把重点放在提升尽可能多Debian包的稳定性和性能。对于初学编程的人来说这是一个很好的起点，Raspbian自带的x windows，因此可以使用它类似WIN风格的GUI界面，Raspbian还包括一个“Pi商店”，你可以免费或付费下载一些应用，比如Libre Office、Free Civ（游戏）等等。

树莓派在一般情况下可以采用Raspian系统。其他可用操作系统可以参考一下网址：
[http://wiki.nxez.com/rpi:list-of-oses](http://wiki.nxez.com/rpi:list-of-oses)

## 必备软件 ##

* 镜像烧录工具（有一个即可）
    + Win32DiskImager(仅windows)
    + Etcher（跨平台）
    + USB Image Tool

## 系统安装 ##

我们首先需要准备一张SD卡，由于我之后打算使用U盘启动，所以SD卡不用准备的太大，能写入镜像就可以了。本文章使用的镜像是2017-09-07-raspbian-stretch-lite.img，一共不到2G，所以我只选用了一个4G的SD卡。如果使用的桌面版的Raspian，最好选用8GB和更大的SD卡。

然后，我们使用Etcher将镜像文件写入。这个过程很简单，可以参考：官方指南：[https://www.raspberrypi.org/documentation/installation/installing-images/README.md](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

## 使用U盘启动（BETA） ##

目前我们手中的U盘应该比SD更多，所以我非常希望使用U盘取代SD卡来启动，Raspian的国外大神真有做到这点的。

国外的大神发现，博通芯片2835/6/7内部其实有一个小的boot ROM。这个小boot ROM从SD卡中读取一个叫bootcode.bin的文件，然后在执行它，接下来就可以从SD卡中载入剩余的系统，比如固件和ARM内核。但是，其实BCM芯片是可以支持从U盘启动的，只不过默认不开启。所以我们需要先做一个系统进入树莓派，通过OTP开启USB boot功能。

当我们从SD卡启动树莓派后，首先执行

```shell
me~$ sudo apt-get update && sudo apt-get upgrade
```

如果安装的镜像是2017-04-10以后的Raspbian 则不用``sudo apt-get upgrade``。

找到``/boot``文件夹，执行：

```shell
me~$ echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt
```

这样将usb boot模式开启。接着``sudo reboot``重启树莓派，开机后执行

```shell
me~$ vcgencmd otp_dump | grep 17:
17:3020000a
```

如果你输出的是``0x3020000a``那说明可以使用USB启动了。**注意config.txt文件结尾不要空格。**
接下来，要将2017-04-10日期之后 Raspbian镜像烧录到U盘中，和烧录的SD卡中的步骤一样。再把U盘插入树莓派，拔掉SD卡就可以执行中U盘启动了。自己**感觉**（未测试）从U盘启动的树莓派要比从SD卡慢一些。

几点注意：

* 仅树莓派3支持使用U盘启动
* 文件系统格式最好为FAT32
* 并不是所有的U盘都作为启动盘，明确可用的U盘有：
    + Sandisk Force 8GB（本人使用，亲测可用）
    + Sandisk Cruzer Fit 16GB
    + Sandisk Cruzer Blade 16Gb
    + Samsung 32GB USB 3.0 drive
    + MeCo 16GB USB 3.0

## U盘复原 ##

被当成树莓派系统盘的U盘通常会被划分成``/boot``和实际存储区，我们在电脑上插入U盘的时候，这回显示``/boot``分区，不能显示完整的U盘空间。如果不在需要这个系统U盘，就需要还原U盘大小。

1. 打开cmd 输入diskpart
2. 非常重要：右键我的电脑-->管理-->磁盘管理。查看磁盘信息，一般情况下U盘会显示成磁盘1。
3. 在diskpart的cmd中输入 select disk 1 (非常重要，这个磁盘一定要是U盘的磁盘)。
4. 输入clean删除其他分区。（如果之前磁盘选错，会删除电脑中其他分区！！！）
5. U盘已经恢复原来大小，选择合适的格式将他格式化，就可以重新当U盘使用了。

## 疑问 ##

如果我直接把镜像写到U盘里，然后修改config.txt是否就不需要用SD卡现状个系统了？？
> 显然不可能。树莓派默认直接中SD卡启动，如果上来就插U盘的系统，树莓派的usb boot根本没Enable。