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

适应函数,原理是将本来不可限制的值域映射到一个指定的范围中。最简单的适应函数就是按比例归一化，将值域映射到(0,1)上。公式表达为

$F(x_i) =\frac{f(x_i)}{\sum_{i=1}^{N_{ind}} f(x_i)}$

其中，$N_{ind}$是种群的数量，$x_i$是每一个基因的表现性（即二进制编码对应目标函数定义域的值）。这种适应函数直观容易理解，每一个个体繁殖的概率和它的适应能力成正比。换一个通用的表达式：

$F(x)=af(x)+b$

其中，a是缩放因子，我们如果求目标最大值，则a为正；反之，a为负。b为偏移量，用来保证$F(x)$非负。但是这种适应函数对于负数无能为力，**同时容易快速收敛（这会降低找到全局最优的概率）**。因为在完全线性的变换中，一旦一个种群在早期出现一个明显优秀的个体，那么根据繁殖概率正比于适应能力，这个个体将主导种群的繁衍，如果这个个体是一个局部最优，这种线性变化能以跳出局部最优的限制。**scaling**函数就是这种使用$F(x)=af(x)+b$的适应函数，这个变换的优点明显：简单易用。scaling函数的参数计算有些难以理解的地方，也不推荐使用，这里按下不表。

## Ranking ##

### 线性适应函数 ###

于是，有很多人提出了其他的适应函数。在GA工具箱中，大体分为线性和非线性两类。线性适应函数形如：，Baker$^{[2]}$提出了基于限定范围和划分等级的适应函数（大概是这个意思）。首先，选定一个MAX值，用来决定对最好个体的偏好（这个值在文献也有被翻译为**压差，selective pressure**），然后规定了以下几个值：

* $MIN = 2.0 - MAX$
* $INC = (MAX -MIN)/N_{ind} = 2.0 * (MAX-1.0)/N_{ind}$
* $LOW = INC / 2.0$

MAX通常取在[1.1,2.0]之间，MIN表示下界。将[MIN,MAX]划分成$N_{ind}$份，INC表示相邻两个等级之间差距，LOW表示选择的次数？？。因此，可以将目标函数做如下变化：

$F(x_i) = MIN + 2(MAX-1.0)\frac {x_i-1}{N_{ind}-1}$ 可以看出后面一项是INC的变形，分成$N_{ind}-1$份是因为包括了上下边界。

这个式子中最重要的${x_i}$，这个是将$N_{ind}$个个体按照排序（从大到小，或者从小到大，根据求最大还是最小值）之后，在排序中的位置。

### 非线性适应函数 ###

非线性函数使用的是指数分割的方法。

### 适应值的分配 ###

我们如果不考虑多种群的场景，函数中适应值的分配很简单，按照目标值大小分配适应值。

### 实现代码 ###

```matlab
function FitnV = cxranking(ObjV, RFun, SUBPOP)

    %Identify the vector size (Nind)
    [Nind,~] = size(ObjV);

    %第二个参数的处理
    if nargin < 2, RFun = [] ;end %仅有一个参数，后面会有默认赋值
    if nargin > 1 % 不合理RFun的处理
        if isnan(RFun), RFun = [];end
    end
    % numel函数返回的是矩阵中元素的个数,增强性能
    if numel(RFun) == 2 % RFun 为1行2列的向量
        if RFun(2) == 1, NonLin = 1; %判断线性还是非线性
        elseif RFun(2) == 0, NonLin = 0;
        else error('Parameter for ranking method must be 0 or 1');
        end
        RFun = RFun(1);
        if isnan(RFun), RFun = 2; end
    elseif numel(RFun) >2, % RFun是一个列向量，列向量和ObjV对应 
        if numel(RFun) ~= Nind, error('ObjV and RFun disagree'); end
    end

    %第三个参数分组的处理
    if nargin < 3,  SUBPOP = 1; end
    if nargin > 2,
      if isempty(SUBPOP), SUBPOP = 1;
      elseif isnan(SUBPOP), SUBPOP = 1;
      elseif length(SUBPOP) ~= 1, error('SUBPOP must be a scalar'); 
      end
    end    
    %分组必须能够整除总数
    if (Nind/SUBPOP) ~= fix(Nind/SUBPOP), error('ObjV and SUBPOP disagree'); end
    Nind = Nind/SUBPOP;  % Compute 

    % Check ranking function and use default values if necessary
    if isempty(RFun)       %为空的时候，采用默认值
        % 默认值：selective pressure： 2，线性
        RFun = 2* [0:Nind-1]'/(Nind-1);
    elseif numel(RFun) == 1  %RFun在之前已经处理过，变成一个标量
        if NonLin == 1 
            %非线性处理,指数分割
            if RFun(1) < 1, error('Selective pressure must be greater than 1');
            elseif RFun(1) > Nind-2, error('Selective pressure too big'); 
            end
            Root1 = roots([RFun(1)-Nind [RFun(1)*ones(1,Nind-1)]]);
            %指数分割
            RFun = (abs(Root1(1)) * ones(Nind,1)) .^ [(0:Nind-1)'];
            RFun = RFun / sum(RFun) * Nind;
        else
            %线性处理
            % linear ranking with SP between 1 and 2
            if (RFun(1) < 1 || RFun(1) > 2),
                error('Selective pressure for linear ranking must be between 1 and 2');
             end
             RFun = 2-RFun + 2*(RFun-1)*[0:Nind-1]'/(Nind-1);
        end
    end
    
       FitnV = [];

       %子群处理
% loop over all subpopulations
for irun = 1:SUBPOP,
   % Copy objective values of actual subpopulation
      ObjVSub = ObjV((irun-1)*Nind+1:irun*Nind);
   % Sort does not handle NaN values as required. So, find those...
      NaNix = isnan(ObjVSub);
      Validix = find(~NaNix);
   % ... and sort only numeric values (smaller is better).
      [~,ix] = sort(-ObjVSub(Validix));

   % Now build indexing vector assuming NaN are worse than numbers,
   % (including Inf!)...
      ix = [find(NaNix) ; Validix(ix)];
   % ... and obtain a sorted version of ObjV
      Sorted = ObjVSub(ix);

   % Assign fitness according to RFun.
      i = 1;
      FitnVSub = zeros(Nind,1);
      for j = [find(Sorted(1:Nind-1) ~= Sorted(2:Nind)); Nind]',
         FitnVSub(i:j) = sum(RFun(i:j)) * ones(j-i+1,1) / (j-i+1);
         i =j+1;
      end

   % Finally, return unsorted vector.
      [~,uix] = sort(ix);
      FitnVSub = FitnVSub(uix);

   % Add FitnVSub to FitnV
      FitnV = [FitnV; FitnVSub];
end


% End of function
```

### 未完成：多种群 ###



## 参考文献 ##

[1] K. A. De Jong, Analysis of the Behaviour of a Class of Genetic Adaptive Systems, PhD Thesis, Dept. of Computer and Communication Sciences, University of Michigan, Ann Arbor, 1975.

[2] J. E. Baker, “Adaptive Selection Methods for Genetic Algorithms”, Proc. ICGA 1, pp. 101-111, 1985.