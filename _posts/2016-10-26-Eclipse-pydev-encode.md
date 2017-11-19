---
layout: post
title:  "转载：Eclipse+pydev解决中文显示和注释问题的方法大全"
categories: Tools
tags:  Tools Python
author: 海木棉枫
---

* content 
{:toc}

原文地址：[http://blog.sina.com.cn/s/blog_779eecd801011j7x.html](http://blog.sina.com.cn/s/blog_779eecd801011j7x.html) 作者：海木棉枫

## Eclipse的设置 ##

window->preferences->general->editors->text editors->spelling->encoding->UTF-8，编辑器的编码格式

window->preferences->workspace->text file encoding->UTF-8

打开eclipse安装目录->eclipse.ini，末行加上”-Dfile.encoding=UTF-8”

excerpt_separator_here

## 文件编码 ##

py文件记得保存成UTF-8，文件首行加上”#coding=utf-8”   ，这一句话可控制代码中可输入中文字符

## run时设置 ##

run-->run configurations->python run->Common-> Encoding ->UTF-8.这个应该是运行时的可解决中文乱码问题。

更改空白模块默认显示# -*- coding: utf-8 -*-


如果想每次新建一个空模块时自动添加”# -*- coding: utf-8 -*-”   这样的一句话，可以通过window--Preferences--Pydev--Editor--Template--Empty，然后点击“Edit”按钮，把我们要添加的语句加进去就可以了，将事先默认的语句去掉，改写为：# -*- coding: utf-8 -*-  这样的一句话,然后你再新建一个空白模块，再也不需要每次都要复制那个编码语句了。

当在建立的python项目时，输入的中文太细，可以通过
Window > Preferences>General>Appearance>Color and Fonts中的第一个来设置，Basic里面的TextFonts设置大小即可。
