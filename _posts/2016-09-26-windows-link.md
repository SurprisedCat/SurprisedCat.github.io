---
layout: post
title:  "转载：Windows 的快捷方式，符号链接，软链接和硬链接"
categories: Windows
tags:  Windows
author: AlphaTr
---

* content 
{:toc}

原文地址：[https://blog.alphatr.com/windows-mklink.html](https://blog.alphatr.com/windows-mklink.html) 作者：AlphaTr

在我之前的印象中，Linux 下面的 ln 命令好像类似于 Windows 下面的快捷方式，但是在接触了 Windows 下面的 mklink 命令后，发现 Windows 下面的链接还是很强大的。

## Linux 下面的 ln 命令 ##

linux 下面的 ln 命令还是很强大的，可以创建软链接和硬链接，使用方式如下：

```shell
ln -s source dist        # 建立软连接
ln source dist           # 建立硬连接
```

关于 Linux 下面的软链和硬链，可以看看这篇文章：[理解 Linux 的硬链接与软链接](https://www.ibm.com/developerworks/cn/linux/l-cn-hardandsymb-links/).
<!--excerpt_separator_here-->

## Windows 下面的链接 ##

Windows 7 下面，在 NTFS 文件系统下面，如果把快捷方式也算是一种链接的话，共有快捷方式，符号链接，软链接和硬链接四种方式。

### 快捷方式 ###

快捷方式应该都是很熟悉的，有指向本地文件的和指向 Web Url 的之分，而且不受分区等的影响，使用的是系统的绝对路径，并且双击快捷方式也会跳到它指向文件的环境来做一些事情。

快捷方式就是普通的文件，只是后缀分别使用了 lnk 和 url 两种，分别指代指向本地文件和网络的快捷方式，而且这两种后缀在普通情况下是没办法显示出来的，可以在命令行模式使它们显示出来，使用一些文本编辑器打开它们，可以看到有一部分是它指向文件的路径。

### Windows 下面的 mklink 命令 ###

打开命令行，直接输入 mklink 可以看到输出 mklink 的帮助信息
创建符号链接。

```bash
MKLINK [[/D] | [/H] | [/J]] Link Target

        /D      创建目录符号链接。默认为文件
                符号链接。
        /H      创建硬链接，而不是符号链接。
        /J      创建目录联接。
        Link    指定新的符号链接名称。
        Target  指定新链接引用的路径
                (相对或绝对)。
```

可以看到，功能还是蛮多的，大概有下面几种链接方式
MKLINK Link Target # 创建指向文件的符号链接
MKLINK /D Link Target # 创建指向文件夹的符号链接
MKLINK /J Link Target # 创建指向文件夹的软链接(联接)
MKLINK /H Link Target # 创建指向文件的硬链接

### 符号链接 ###

符号链接支持文件和文件夹，和快捷方式的区别就是快捷方式会跳回自己的环境，而符号链接不会跳回，而是使用创建后的环境，例如分别创建名为 link 的符号链接和快捷方式指向 a 文件，分别打开 link 文件，符号链接显示的文件名是 link，而快捷方式显示的是 a。符号链接指向的文件更新后，原文件也会跟着更新。还有一个，Windows 下面创建的符号链接就相当于 Linux 下面的符号链接(软链接)。

### 软链接 ###

软链接只是支持文件夹的链接，而不支持文件的链接，和符号链接的区别就是符号链接在创建时候可以使用相对路径和绝对路径，创建成功后也就是对应的相对路径和绝对路径，绝对路径在原文件(夹)不移动的情况下都可以，而相对路径是相对于两个文件的路径，所以两个文件的相对位置没有改变就不会链接错误，而软链接不管在创建的时候使用的是相对路径还是绝对路径，创建后全部转换为绝对路径。另外一个区别就是，符号链接属性是一个快捷方式类似的，而软链接类型是一个和指向文件没有区别的类型，如下图:

![软链接类型是一个和指向文件没有区别的类型](/assets/windows-links-compare.png)

### 硬链接 ###

同样，和 Linux 一样，在 Windows 下面，硬链接是不支持文件夹(目录)的，这和文件系统是有关系的，硬链接和软链接的区别就是硬链接完全就是一个文件，和从指向的文件是处在同级的，两个文件指向了同一块物理路径而已，所以删除任意一个，对另外一个都没有影响，而且一个文件更新，另外一个也会同样恨着更新。正因为如此，所以硬链接只能创建在同一个分区中。
几个区别有如下的示意图

![几种链接的区别](/assets/windows-links.png)