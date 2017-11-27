---
layout: post
title:  "MatLab遗传算法工具箱（Sheffield）源码解析（2）"
categories: Algorithm
tags:  Algorithm Matlab
author: SurprisedCat
---

## 适应度计算函数 ##

matlab的Sheffield工具箱中，适应度计算函数共有一下两个：
% Fitness assignment
%   ranking	- rank-based fitness assignment
%   scaling	- proportional fitness-scaling
遗传算法中，一个个体(解)的好坏用适应度函数值来评价，在本问题中，f(x)就是适应度函数。适应度函数值越大，解的质量越高。

适应度函数是遗传算法进化的驱动力，也是进行自然选择的唯一标准，它的设计应结合求解问题本身的要求而定。

## ranking ##