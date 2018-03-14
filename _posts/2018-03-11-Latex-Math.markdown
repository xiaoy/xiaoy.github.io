---
layout:     post
title:      "Latex数学速查手册"
subtitle:   "游戏开发数学公式" 
date:   2018-03-11
author: "wulongfei"
tags:
    - Latex
    - Math
    - Game Program math
---

# 前言
游戏文章中要用到很多的数学公式，学习Latex的数学表达式，非常有必要，这里简单记录一下常用的游戏开发中用到的数学公式，在将来的文章中来当速查手册。

# 函数
## 基础成员
a、角度使用($a,b,\theta,\lambda$)

b、向量使用$\mathbf(x,y,z)$

c、矩阵使用$\mathbf(R,S,T,M)$
## 普通函数
### 2元一次方程
$$ f(x) = ax^2 + bx + c $$
### 求值
$$ x =\frac{a \pm \sqrt{b^2-4ac}}{2a} $$

### 求和
$$ \sum_{i=0}^n i^2 = \frac{(n^2 + n)(2n+1)}{6} $$
# 向量
$$ v_1 = [x_1, y_1, z_1] $$
$$ v_2 = [x_2, y_2, z_2] $$
## 向量加法
$$ v_1 + v_2 = [x_1 + x_2,y_1 + y_2,z_1+z_2]$$

## 向量点积
$$ v_1.v_2 = x_1x_2 + y_1y_2 + z_1z_2 $$

$$v_1.v_2 = |v_1||v_2|\cos(\theta)$$
## 向量夹角

$$\theta = \cos^{-1}\frac{v_1v_2}{|v_1||v_2|} $$

## 向量模叉乘
$$|v_1\times v_2| = |v_1||v_2|\sin(\theta)$$

# 矩阵
## 移动
$$
p'=T(v_x, v_y, v_z) . p =
\begin{bmatrix}
1 & 0 & 0 & v_x \\
0 & 1 & 0 & v_y \\
0 & 0 & 1 & v_z \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
p_x \\
p_y \\
p_z \\
1
\end{bmatrix}=
\begin{bmatrix}
p_x + v_x \\
p_y + v_y \\
p_z + v_z \\
1
\end{bmatrix}
$$

## 缩放
$$
p'=S(s_x, s_y, s_z).p=
\begin{bmatrix}
s_x & 0 & 0 & 0\\
0 & s_y & 0 & 0\\
0 & 0 & s_z & 0\\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
p_x \\
p_y \\
p_z \\
1
\end{bmatrix}=
\begin{bmatrix}
s_xp_x \\
s_yp_y \\
s_zp_z \\
1
\end{bmatrix}
$$
## 旋转
### 绕$X$轴旋转
$$
p' = R_x(\theta) = 
\begin{bmatrix}
1 & 0 & 0  & 0 \\
0 & \cos(\theta) & -\sin(\theta) & 0\\
0 & \sin(\theta) & \cos(\theta) & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
p_x\\
p_y\\
p_z\\
0
\end{bmatrix}=
\begin{bmatrix}
p_x\\
\cos(\theta)p_y -\sin(\theta)p_z\\
\sin(\theta)p_y + \cos(\theta)p_z\\
0
\end{bmatrix}
$$

### 绕$Y$轴旋转
$$
p' = R_y(\theta) = 
\begin{bmatrix}
\cos(\theta) & 0 & \sin(\theta) & 0\\
0 & 1 & 0  & 0 \\
-\sin(\theta) & 0 & \cos(\theta) & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
p_x\\
p_y\\
p_z\\
0
\end{bmatrix}=
\begin{bmatrix}
\cos(\theta)p_x + \sin(\theta)p_z\\
p_y\\
-\sin(\theta)p_x + \cos(\theta)p_z\\
0
\end{bmatrix}
$$


### 绕$Z$轴旋转
$$
p' = R_z(\theta) = 
\begin{bmatrix}
 \cos(\theta) & -\sin(\theta) & 0 & 0\\
 \sin(\theta) & \cos(\theta) & 0 & 0\\
 0 & 0  & 1 & 0\\
 0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
p_x\\
p_y\\
p_z\\
0
\end{bmatrix}=
\begin{bmatrix}
\cos(\theta)p_x - \sin(\theta)p_y\\
\sin(\theta)p_x + \cos(\theta)p_y\\
p_z \\
0
\end{bmatrix}
$$
