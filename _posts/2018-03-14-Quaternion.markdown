---
layout:     post
title:      "四元数"
subtitle:   "四元数笔记" 
date:   2018-03-11
author: "wulongfei"
tags:
    - Latex
    - Math
    - Game Program math
---
# 前言
在我刚开始做游戏的时候，在学习到三维物体旋转时第一次遇到四元数，那时候只学习到使用欧拉角旋转会遇到旋转死锁的问题，四元数可以解决。自己那时候学习了下四元数，感觉好难理解。最后照着Unity官方文档，只做到了会用，我在学习新东西时候总是想把原理搞个清楚，这样心里才会舒服。这个四元数的问题一直如鲠在喉，最近在看《游戏引擎架构》，对于里面每一个知识点都不想放过去，所以就有了这篇文章。
# 四元数
## 复数
要说明白四元数，首先得搞清楚复数的概念，数学家为了解决对负数开平方的问题，引入了复数。

$$z = a + bi$$

其中$i^2 = -1$
## 复数
如果将二维复数扩展到三维，就是四元数

$$ q = s + xi + yj + zk$$

## 有序对表达式
$$ q = [s, v] $$

v可以单独为组件

$$ q = [s, xi + yj + zk] $$
## 加法
$$
\begin{array}{rcl}
q_a & = & [s_a,\mathbf{a}] \\
q_b & = & [s_b,\mathbf{b}] \\
q_a+q_b & = & [s_a+s_b,\mathbf{a}+\mathbf{b}] \\
q_a-q_b & = & [s_a-s_b,\mathbf{a}-\mathbf{b}]\end{array}
$$
## 四元数乘法
$$
\begin{array}{rcl}
q_a & = & [s_a,\mathbf{a}] \\
q_b & = & [s_b,\mathbf{b}] \\
q_aq_b & = & [s_a,\mathbf{a}][s_b,\mathbf{b}] \\
&= &(s_a+ x_ai + y_aj + z_ak)(s_b + x_bi + y_bj + z_bk) \\
&= &(s_as_b - x_ax_b - y_ay_b - z_az_b) \\
& &+(s_ax_b + s_bx_a + y_az_b  - y_bz_a)i \\
& &+(s_ay_b + s_by_a + z_ax_b - x_az_b)j\\
& &+(s_az_b + x_ay_b - y_ax_b + s_bz_a)k
\end{array}
$$

$$ i = [0,i] ~~~ j = [0, j] ~~~~ k = [0,k]$$
实数部分用$[1, 0]$

$$
\begin{array}{rcl}
[s_a,\mathbf{a}][s_b,\mathbf{b}] 
&= &(s_as_b - x_ax_b - y_ay_b - z_az_b)[1, 0] \\
& &+(s_ax_b + s_bx_a + y_az_b  - y_bz_a)[0, i] \\
& &+(s_ay_b + s_by_a + z_ax_b - x_az_b)[0, j]\\
& &+(s_az_b + s_bz_a + x_ay_b - y_ax_b)[0,k]
\end{array}
$$

展开

$$
\begin{array}{rcl}
[s_a,\mathbf{a}][s_b,\mathbf{b}] 
&= &[s_as_b - x_ax_b - y_ay_b - z_az_b, 0] \\
& &+[0,(s_ax_b + s_bx_a + y_az_b  - y_bz_a)i] \\
& &+[0,(s_ay_b + s_by_a + z_ax_b - x_az_b)j]\\
& &+[0,(s_az_b + s_bz_a + x_ay_b - y_ax_b)k]
\end{array}
$$

虚数部分结合

$$
\begin{array}{rcl}
[s_a,\mathbf{a}][s_b,\mathbf{b}] 
&= &[s_as_b - x_ax_b - y_ay_b - z_az_b, 0] \\
&+&[0,s_a(x_bi + y_bj + z_bk) + s_b(x_ai + y_aj + z_ak) \\
&+&(y_az_b - y_bz_a)i + (z_ax_b - x_az_b)j + (x_ay_b - y_ax_b)k]\\
\end{array}
$$

最终结果

$$
\begin{array}{rcl}
[s_a,\mathbf{a}][s_b,\mathbf{b}] 
&= &[s_as_b - x_ax_b - y_ay_b - z_az_b, \\
&&s_a(x_bi + y_bj + z_bk) + s_b(x_ai + y_aj + z_ak) \\
&+&(y_az_b - y_bz_a)i + (z_ax_b - x_az_b)j + (x_ay_b - y_ax_b)k]\\
\end{array}
$$

当前设定

$$
\begin{array}{rcl}
\mathbf{a}~ = & x_ai + y_aj + z_ak \\
\mathbf{b}~ = & x_bi + y_bj + z_bk \\
\mathbf{a.b}~ = & x_ax_b + y_ay_b + z_az_b \\
\mathbf{a}\times\mathbf{b}~ = &(y_az_b - y_bz_a)i + (z_ax_b - x_az_b)j + (x_az_b - z_ax_b)k
\end{array}
$$

带入得到结果

$$
\begin{array}{rcl}
[s_a,\mathbf{a}][s_b,\mathbf{b}] = [s_as_b - \mathbf{a.b}, s_a\mathbf{b} + s_b\mathbf{a} + \mathbf{a}\times\mathbf{b}]
\end{array}
$$

## 实数四元数
$$
\begin{array}{rcl}
q & = &[s, 0] \\
q_a & = &[s_a, 0] \\
q_b & = &[s_b, 0] \\
q_aq_b & = &[s_as_b, 0]
\end{array}
$$
## 虚数四元数
$$
\begin{array}{rcl}
q & = &[0, v] \\
q_a & = &[0, v_a] \\
q_b & = &[0, v_b] \\
q_aq_b & = &[-v_av_b, v_a\times\mathbf{v_b}] 
\end{array}
$$

## 共轭
$$
\begin{array}{rcl}
q & = &[s, v] \\
q^* & = &[s, -v] \\
qq^* &= &[s^2+v^2, 0]
\end{array}
$$

## 归一化
$$
q^{\prime} =  \frac{q}{\sqrt{s^2 + v^2}}
$$

## 逆四元数
$$
qq^{-1} = [1, 0]
$$
$$
q^{-1} = \frac{q^{*}}{{|q|}^{2}}
$$

## 点积
$$
\begin{array}{rcl}
q_1 & = & [s_1, x_1i + y_1j + z_1k] \\
q_2 & = & [s_2, x_2i + y_2j + z_2k] \\
q_1{\cdot}q_2 & = & s_1s_2 + x_1x_2 + y_1y_z + z_1z_2\\
\end{array}
$$

余弦

$$
cos{\theta} = \frac{q_1.q_2}{|q_1||q_2|}
$$
## 旋转
### 2d 旋转
$$
q = cos{\theta} + sin{\theta}i
$$

### 3d 旋转
$$
q = [cos{\theta}, sin{\theta}\mathbf{v}]  
$$

### 旋转
矢量p用四元数表示为纯虚数为:

$$
p = [0, \mathbf{p}] 
$$

旋转四元数为:

$$
q = [s, \lambda\hat{v}] 
$$

乘法

$$
\begin{array}{rcl}
p^{\prime} &=& qp \\
&=& [s, \lambda\hat{v}][0, \mathbf{p}] \\
&=& [-\lambda\hat{v}\cdot\mathbf{p}, s\cdot\mathbf{p} + \lambda\hat{v}\times\mathbf{p}]
\end{array}
$$

### 旋转例子一
绕$k$旋转45°

$$
\begin{array}{rcl}
p &=& [0, 2i] \\
q &=& [\cos{\theta}, \sin{\theta}\hat{v}] \\
\hat{v} &=& [0, k] \\
p^{\prime} &=&\left[\frac{\sqrt{2}}{2}, \frac{\sqrt{2}}{2}k][0,2i\right] \\
&=& \left[0, \sqrt{2}i + \sqrt{2}j\right]
\end{array}
$$

### 旋转例子二
绕$jk$的中间轴旋转45˚

$$
\begin{array}{rcl}
p &=& [0, 2i] \\
q &=& [\cos{\theta}, \sin{\theta}\hat{v}] \\
\hat{v} &=& \left[0, \frac{\sqrt{2}}{2}i + \frac{\sqrt{2}}{2}k\right] \\
\end{array}
$$

计算结果为:

$$
\begin{array}{rcl}
p^{\prime} &=& \left[-\frac{\sqrt{2}}{2}\left(\frac{\sqrt{2}}{2}\mathbf{i}+\frac{\sqrt{2}}{2}\mathbf{k}\right)\cdot2\mathbf{i},\frac{\sqrt{2}}{2}2\mathbf{i}+\frac{\sqrt{2}}{2}\left(\frac{\sqrt{2}}{2}\mathbf{i}+\frac{\sqrt{2}}{2}\mathbf{k}\right)\times2\mathbf{i}\right] \\
&=& [-1, \sqrt{2}\mathbf{i} + j]
\end{array}
$$

矢量表示为纯虚数，旋转结果不是纯虚数，模变为 $\sqrt{3}$

现在来求q的逆矩阵

$$
\begin{array}{rcl}
q^{-1} &=& [\cos{\theta}, -\sin{\theta}\hat{v}] \\
q^{-1} &=& \left[\frac{\sqrt{2}}{2}, -\frac{1}{2}i -\frac{1}{2}k\right]
\end{array}
$$

乘逆矩阵:

$$
\begin{array}{rcl}
p^{\prime}q^{-1} &=& \left[-1, \sqrt{2}i + j\right]\left[\frac{\sqrt{2}}{2}, -\frac{1}{2}i -\frac{1}{2}k\right] \\
&=&\frac{1}{2}\left[-\sqrt{2} + \sqrt{2},i+k+2i +\sqrt{2}j -i +\sqrt{2}j+k\right] \\
&=&\frac{1}{2}\left[0, 2k + 2i + 2\sqrt{2}j\right] \\
&=&[0,i +\sqrt{2}j + k]
\end{array}
$$
