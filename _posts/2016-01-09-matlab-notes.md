---
layout: post
title:  "MatLab使用的Tips"
categories: Matlab
tags:  Matlab Tools
author: SurprisedCat
---

* content 
{:toc}

## Matlab使用TIPs ##

* Matlab安装新的工具箱，以Sheffield（设菲尔德大学）遗传算法工具箱为例。

> 1. 解压gatbx-origin.zip，得到DOC和SRC文件夹；
2. 拷贝SRC到Matlab安装目录下的toolbox文件夹中，并将SRC更名为genetic；
3. 在Matlab的setpath中添加genetic所在的位置
4. v=ver('genetic')检验是否成功安装。如果成功：

```matlab
>> v=ver('genetic')

v =

       Name: 'Genetic Algorithm Toolbox'
    Version: '1.2'
    Release: ''
       Date: '15-Apr-94'

```