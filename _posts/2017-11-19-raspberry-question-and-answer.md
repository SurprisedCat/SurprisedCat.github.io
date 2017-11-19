---
layout: post
title:  "树莓派常见问题解决（有问题时常更新）"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

本页面记录了使用树莓派的过程中遇到的一些问题和解决方案。

1. 安装好系统后没有显额外示器，新版本树莓派默认关闭ssh服务器，无法连接上树莓派。
> 在SD卡的/boot中，添加一个名为“ssh”的空文件，注意不用使用windows自带的文本编辑器动它！
2. 树莓派连接上显示器之后没有反应。
> 修改sd卡/boot分区中的config.txt文件。


## 参考文献 ##