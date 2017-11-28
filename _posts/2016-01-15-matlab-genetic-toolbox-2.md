---
layout: post
title:  "MatLab遗传算法工具箱（Sheffield）源码解析（2）"
categories: Algorithm
tags:  Algorithm Matlab
author: SurprisedCat
---

## 适应度计算函数 ##

## 目标函数与适应度函数 ##

目标函数和适应度函数是GA算法中非常重要的两个概念。目标函数（objective function），是衡量一个种群的个体好坏的判别式。通常，目标函数是我们需要解决的问题。例如在$f(x)=\frac{sin(10\pi x)}{x}$,$f(x)$就是目标函数，我们可以直接通过目标函数判断某个个体的好坏，比如求$min_{f(x)}$,适应度最好的个体就是使$f(x)$最小的个体。

但是，$f(x)$的值域是不确定的，在一个函数中很大的值在另一个函数中有可能只是沧海一粟，绝对值的比较在GA算法中意义不太，而且直接用目标函数的值去进行“选择”操作也不太好操作，因此需要将$f(x)$的值做一些规划与变换，将$f(x)$的值由绝对值转换成相对值，即$F(x)=g(f(x))$,$F(x)$表示相对的适应度，这个变化的函数$g(\cdot )$，我们称之为**适应函数(fitness function)**。GA Manual中的解释为：
> The fitness function, is normally used to transform the objective function value into a measure of relative fitness.$^{[1]}$
<!--excerpt_separator_here-->

matlab的Sheffield工具箱中，适应度计算函数共有一下两个：

```matlab
% Fitness assignment
%   ranking	- rank-based fitness assignment
%   scaling	- proportional fitness-scaling
```

## Scaling ##

**scaling**函数是最容易的适应函数,原理很简单就是按比例归一化。公式表达为

$F(x_i) =\frac{f(x_i)}{\sum_{i=1}^{N_{ind}} f(x_i)}$

其中，${N_{ind}$是种群的数量，$x_i$是每一个基因的表现性（即二进制编码对应目标函数定义域的值）。这种适应函数直观容易理解，每一个个体繁殖的概率和它的适应能力成正比，但是这种适应函数对于负数无能为力。

## Ranking ##

为了克服scaling函数的缺点，有很多人提出了其他的适应函数。在GA工具箱中，大体分为线性和非线性两类。

### 线性适应函数 ###

线性适应函数形如：

$F(x)=af(x)+b$

其中，a是缩放因子，我们如果求目标最大值，则a为正；反之，a为负。b为偏移量，用来保证$F(x)$非负。线性适应函数可以有效的对负值进行处理，但是却容易快速收敛（这会降低找到全局最优的概率）。再此基础上，Baker$^{[2]}$提出了基于限定范围和划分等级的适应函数（大概是这个意思）。首先，选定一个MAX值，用来决定对最好个体的偏好（这个值在文献也有被翻译为**压差，selective pressure**），然后规定了以下几个值：

* $MIN = 2.0 - MAX$
* $INC = (MAX -MIN)/N_{ind} = 2.0 * (MAX-1.0)/N_{ind}$
* $LOW = INC / 2.0$

MAX通常取在[1.1,2.0]之间，MIN表示下界。将[MIN,MAX]划分成$N_{ind}$份，INC表示相邻两个等级之间差距，LOW表示选择的次数？？。因此，可以将目标函数做如下变化：

$F(x_i) = MIN + 2(MAX-1.0)\frac {x_i-1}{N_{ind}-1}$ 可以看出后面一项是INC的变形

这个式子中最重要的${x_i}$，这个是将$N_{ind}$个个体按照排序（从大到小，或者从小到大，根据求最大还是最小值）之后，在排序中的位置。

### 非线性适应函数 ###


### 未完成：多种群 ###

## 参考文献 ##

[1] K. A. De Jong, Analysis of the Behaviour of a Class of Genetic Adaptive Systems, PhD Thesis, Dept. of Computer and Communication Sciences, University of Michigan, Ann Arbor, 1975.

[2] J. E. Baker, “Adaptive Selection Methods for Genetic Algorithms”, Proc. ICGA 1, pp. 101-111, 1985.