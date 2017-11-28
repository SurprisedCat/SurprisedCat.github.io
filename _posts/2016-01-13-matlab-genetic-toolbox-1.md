---
layout: post
title:  "MatLab遗传算法工具箱（Sheffield）源码解析（1）"
categories: Algorithm
tags:  Algorithm Matlab
author: SurprisedCat
---

* content
{:toc}

## 创建种群相关函数 ##

crtbase：创建一个包含基因基因座信息的向量。

<!--excerpt_separator_here-->

## crtbase ##

crtbase：创建一个包含基因基因座信息的向量。

关于这个函数的释义，原文文档解释如下：

```matlab
% CRTBASE.m - Create base vector 
%
% This function creates a vector containing the base of the loci
% in a chromosome.
%
% Syntax: BaseVec = crtbase(Lind, Base)
%
% Input Parameters:
%
%		Lind	- A scalar or vector containing the lengths
%			  of the alleles.  Sum(Lind) is the length of
%			  the corresponding chromosome.这句话很重要Lind的和是基因的长度。
%
%		Base	- A scalar or vector containing the base of
%			  the loci contained in the Alleles.
%
% Output Parameters:
%
%		BaseVec	- A vector whose elements correspond to the base
%			  of the loci of the associated chromosome structure.

% Author: Andrew Chipperfield
% Date: 19-Jan-94
```

但是看了源码之后，我发现一些疑似bug的地方，下文用将会标注出来，我自己修正了一下。这个项目是九几年的，而且matlab也出了自己的遗传算法工具箱gatool，所以这个bug权当是学习了。

```matlab
function BaseVec = crtbase(Lind, Base)
%使用逗号区分返回值
[ml,LenL] = size(Lind);
if nargin <2 % 只有一个参数执行
    %这里ones()的行列是相反的
    Base = 2 * ones(1,LenL);
end
%使用逗号区分返回值
[mb,LenB] = size(Base);

% check parameter consistency
% 使用||和&&替换|和&
if ml >1 || mb > 1 % 限定必须都是一维行向量
    error('Lind or Base is not a vector');
elseif (LenL > 1 && LenB > 1 && LenL ~= LenB) || (LenL == 1 && LenB >1)
    error('Vector dimension must agree');
elseif LenB == 1 && LenL >1
    %这里的ones()行列是相反的
    Base = Base * ones(1,LenL);
end

BaseVec = [];
for i = 1:LenL
    %这里ones()的行列是相反的
    BaseVec = [BaseVec, Base(i)*ones(1,Lind(i))];
end
```

## crtbp ##

crtbp:创建任意进制的离散随机种群。这个函数可有三种格式。

1. [Chrom, Lind, BaseV] = crtbp(Nind, Lind)。 创建一个Nind*Lind的随机二进制矩阵。Nind与Lind都是标量，Nind表示种群中个体数量，Lind表示基因长度。
2. [Chrom, Lind, BaseV] = crtbp(Nind, Base)。创建一个Nind*Base长度的矩阵。Nind是标量，Base是矢量，base中的值，表示基因位的进制数。或者Nind是矢量，第一位表示个数，第二位表示基因长度Base是矢量，base中的值，表示基因位的进制数。
3. [Chrom, Lind, BaseV] = crtbp(Nind, Lind, Base)。创建一个Nind*Lind的随机矩阵，每一位的进制数由Base决定。

关于这个函数的释义，原文文档解释如下：

```matlab
% crtbp.m - Create an initial population
%
% This function creates a binary population of given size and structure.
%
% Syntax: [Chrom Lind BaseV] = crtbp(Nind, Lind, Base)
%
% Input Parameters:
%
%		Nind	- Either a scalar containing the number of individuals
%			  in the new population or a row vector of length two
%			  containing the number of individuals and their length.
%
%		Lind	- A scalar containing the length of the individual
%			  chromosomes.
%
%		Base	- A scalar containing the base of the chromosome 
%			  elements or a row vector containing the base(s) 
%			  of the loci of the chromosomes.
%
% Output Parameters:
%
%		Chrom	- A matrix containing the random valued chromosomes 
%			  row wise.
%
%		Lind	- A scalar containing the length of the chromosome.
%
%		BaseV	- A row vector containing the base of the 
%			  chromosome loci.

% Author: Andrew Chipperfield
% Date:	19-Jan-94
```

源码解析如下：

```matlab
function [Chrom, Lind, BaseV] = cxcrtbp(Nind, Lind, Base)
nargs = nargin;

%~表示这个变量后面不会用到
if nargs >= 1, [~, nN] = size(Nind) ; end
if nargs >= 2, [~, nL] = size(Lind) ; end
if nargs == 3, [~, nB] = size(Base) ; end

if nN == 2 %Nind是一个向量，第一位Nind(1)表示种群数量，第二位Nind(2)表示基因长度。
    if(nargs == 1)%仅有一个参数
        Lind = Nind(2);
        Nind = Nind(1);
        BaseV = cxcrtbase(Lind);
    elseif (nargs == 2 && nL == 1)%Nind是一个向量,两个参数的时候，第二个参数必然是基因位的进制数,并且是一个标量
        BaseV = cxcrtbase(Nind(2),Lind);
        Lind = Nind(2);%Nind(2)其实是基因的长度，Lind原来值是进制数
        Nind = Nind(1);
    elseif (nargs == 2 && nL > 1)%Nind是一个向量,两个参数的时候，第二个参数必然是基因位的进制数,是一个向量
        %BUG 原来的代码if Lind ~= length(Lind), error('Lind and Base disagree'); end
        %这里是想做一个判断，看看基因的长度和基向量的长度是否一致，但是长度应该是Nind（2）而不是Lind，Lind在这个分值里BaseV
        if Nind(2) ~= length(Lind), error('Lind and Base disagree'); end 
        BaseV = Lind ; Lind = Nind(2) ; Nind = Nind(1) ; 
    end
elseif nN == 1  %Nind是一个标量，表示种群数量
    if (nargs == 1)%仅有一个参数
        error('Not enough input arguments.') ;
    elseif nargs == 2 %两个参数，需要看看第二位是标量还是向量
        if nL == 1 %第二个参数是标量。说明是基因长度
            BaseV = cxcrtbase(Lind);
        else % 第二个参数是向量，表示他是基向量
            BaseV = Lind;
            Lind = nL;%基向量的长度表示基因的长度
        end
    elseif nargs == 3 % 第二位 Lind 标量，第三位基因进制信息
        if nB == 1
            BaseV=cxcrtbase(Lind,Base);%第三位标量，表示基因有统一的进制base
        elseif nB ~= Lind
            error('Lind and Base disagree') ;%第三位向量，看看长度是否匹配
        else
            BaseV=Base;%标准形式，第一位Nind，第二位Lind，第三位BaseV
        end
    end 
end

% Create a structure of random chromosomes in row wise order, dimensions
% Nind by Lind. The base of each chromosomes loci is given by the value
% of the corresponding element of the row vector base.

%BaseV(ones(Nind,1),:)表示向量扩展成矩阵，重复扩展Nind行
%floor 向下取整
Chrom = floor(rand(Nind,Lind).*BaseV(ones(Nind,1),:));
```

## crtrp ##

crtrp的功能是产生一个实数值种群,crtbp可以产生一定进制的种群，但都是离散值，crtrp是产生连续值（相对的）的种群。

crtrp的参数相对固定，必须要有两个，第一个Nind表示种群的个数，第二个参数为一个2*Lind的矩阵，分别代表着上下界，Lind为基因的长度。函数的源码介绍如下:

```matlab
% crtrp.m        (CReaTe an initial (Real-value) Population)
%
% This function creates a population of given size of random real-values. 
%
% Syntax:       Chrom = crtrp(Nind,FieldDR);
%
% Input parameters:
%    Nind      - A scalar containing the number of individuals in the new 
%                population.
%
%    FieldDR   - A matrix of size 2 by number of variables describing the
%                boundaries of each variable. It has the following structure:
%                [lower_bound;   (vector with lower bound for each veriable)
%                 upper_bound]   (vector with upper bound for each veriable)
%                [lower_bound_var_1  lower_bound_var_2 ... lower_bound_var_Nvar;
%                 upper_bound_var_1  upper_bound_var_2 ... upper_bound_var_Nvar]
%                example - each individuals consists of 4 variables:
%                FieldDR = [-100 -50 -30 -20;   % lower bound
%                            100  50  30  20]   % upper bound
%              
% Output parameter:
%    Chrom     - A matrix containing the random valued individuals of the
%                new population of size Nind by number of variables.

% Author:     Hartmut Pohlheim
% History:    23.11.93     file created
%             25.02.94     clean up, check parameter consistency
```

源码中输入参数处理的部分比上两个函数简单很多，却有一个明显bug，很多人也提过，就是nargin赋值的问题。同时将rep函数替换成系统自带的repmat函数，提高可靠性与性能。

```matlab
function Chrom = crtrp(Nind,FieldDR)

    nargs = nargin

    % Check parameter consistency
    if nargs < 2, error('parameter FieldDR missing'); end
    %下面这句明显是个错误，nargin是个函数，不能赋值。注释掉，不需要
    % BUG
    %if nargs > 2, nargin = 2; end

    [mN, nN] = size(Nind);
    [mF, Nvar] = size(FieldDR);
    
    %Nind必须是个标量，表示种群个数
    if (mN ~= 1 & nN ~= 1), error('Nind has to be a scalar'); end
    % FieldDR必须是个2行矩阵，第一行为下界，第二行为上界
    if mF ~= 2, error('FieldDR must be a matrix with 2 rows'); end
   
   %  Compute Matrix with Range of variables and Matrix with Lower value
   % 用来生成制定范围的随机数，使用matlab自带的repmat函数代替工具箱中的rep函数
   % repmat（arg1,arg2）函数：将矩阵arg1,扩展行arg2(1)次，列arg2(2)次
   Range = repmat((FieldDR(2,:)-FieldDR(1,:)),[Nind 1]);
   Lower = repmat(FieldDR(1,:), [Nind 1]);
   
   % Create initial population
   % Each row contains one individual, the values of each variable uniformly
   % distributed between lower and upper bound (given by FieldDR)
   Chrom = rand(Nind,Nvar) .* Range + Lower;

% End of function
```

至此，Sheffield工具箱中的种群随机生成函数就这么多了。