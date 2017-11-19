---
layout: post
title:  "转载： linux下printf中\n的重要作用"
categories: C++
tags:  C++ Linux
author: George
---

* content 
{:toc}

原文链接：[http://blog.sina.com.cn/s/blog_563f0ca801000bfe.html](http://blog.sina.com.cn/s/blog_563f0ca801000bfe.html)
作者：George

## linux下printf中\n的重要作用 ##

今天遇到一个很有趣的问题:
我用的是linux 2.6.11
程序大概是这样的(参数不详写了):

```c++
    printf("abc");
    listen(...);
    accept(...);
```

excerpt_separator_here

程序死活打不出"abc"来.用gdb查看,程序又确实是运行到了accept这里。

有大虾告诉我:在abc末尾加\n试试。

加了以后,abc就打出来了。

后来又试了类似的:

```c++
    printf("abc");
    recvfrom(...);
    //程序一样打不出abc来,
    printf("abc\n");
    recvfrom(...);
    //abc就打出来了.
```

大虾说linux下printf是以"行"来做缓冲区来刷新stdout的,如果遇到\n会强制立即刷新。否则刷新可能会延迟。因为printf中没有包含\n,内核决定满一行再刷新,而此时程序由于调用accept或recvfrom这类会block的api函数,造成屏幕一直显示不出来,除非收到了tcp连接请求或数据包,系统才会重新决定是否刷新屏幕。

看来小小的\n还是蛮重要的。此问题可以在<<UNIX网络编程 第2版 第2卷进程间通信>>里找到一些相关知识。多了解了解文件I/O还是很有必要的。