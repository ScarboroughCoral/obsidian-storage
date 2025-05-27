---
title: DIP中常见的核
mathjax: true
copyright: true
comment: true
date: 2019-03-14 19:01:20
tags:
- Computer Graphics
- DIP
categories: 
- Computer Science
- Computer Graphics

---

{% note default %}
一二三四五，六七八九十。
{% endnote %}

<!-- more -->

---

{% note default %}

**文章暂未完成**

{% endnote %}


## 前言

DIP（Digital Image Processing）中的核可以看做是一个模具，可以作用于每个像素。每种核作用图像后会得到不同的结果，本文主要是记录一些常见的“核”

视频见[YOUTOBE](https://www.youtube.com/watch?v=izDu5zdjjYA)

{% note info%}
## 核是什么

核可以看做是一个模具，也叫做模板，有时也称算子。模板操作是数字图像处理中常用的一种邻域运算方式，主要有卷积和相关两种，可以实现图像平滑、图像锐化、边缘检测等功能。

- 核常用矩阵表示。可以是一幅图像、一个滤波器或一个窗口，定义了参与运算的中心元素和邻域元素的相对位置及相关系数。卷积时常采用3×3或者5×5大小。
- 模板的中心元素（或称原点）表示将要处理的元素， 一般取模板中心点，也可根据需要选取非中心点。

{% endnote %}

## 常见的核

{% note primary%}
### 图像平滑
{% endnote %}



#### 邻域平均滤波
> 邻域平均法是一种线性低通滤波器。**可滤除一定的噪声。**

- 第一种

$$
    \frac{1}{9}\times
    \begin{bmatrix}
        1&1&1\\\\
        1&1&1\\\\
        1&1&1
    \end{bmatrix}
$$

- 第二种

$$
    \frac{1}{16}\times
    \begin{bmatrix}
       1&2&1\\\\
       2&4&2\\\\
       1&2&1 
    \end{bmatrix}
$$

#### 中值滤波

> 是一种非线性滤波，**找出核内的中位数当做结果**。

$$
       Midian( 
           \begin{bmatrix}
             a&b&c\\\\
             d&e&f\\\\
             g&h&i 
           \end{bmatrix})
$$


{% note primary%}
### 边缘检测
{% endnote %}

> 用于检测图像中一些物体边缘的地方。比如无人机、自动驾驶障碍物检测等会使用到。

主要方法有以下几种：

#### Prewitt

> 引入了平均因素，对噪声有抑制作用；操作简便。

- 垂直边缘检测算子


$$
           \begin{bmatrix}
             -1&0&1\\\\
             -1&0&1\\\\
             -1&0&1 
           \end{bmatrix}
$$

- 水平边缘检测算子


$$
           \begin{bmatrix}
             -1&-1&-1\\\\
             0&0&0\\\\
             1&1&1 
           \end{bmatrix}
$$
#### Sobel
> 引入了平均因素，增强了最近像素的影像，噪声抑制效果比Prewitt好。

- 垂直边缘检测算子


$$
           \begin{bmatrix}
             -1&0&1\\\\
             -2&0&2\\\\
             -1&0&1 
           \end{bmatrix}
$$

- 水平边缘检测算子


$$
           \begin{bmatrix}
             -1&-2&-1\\\\
             0&0&0\\\\
             1&2&1 
           \end{bmatrix}
$$
