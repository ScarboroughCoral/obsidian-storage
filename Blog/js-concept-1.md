---
title: JavaScript必知33个概念系列：函数调用栈（执行上下文）
mathjax: false
copyright: true
comment: true
date: 2019-11-14 15:21:48
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
JavaScript的执行上下文。
{% endnote %}

<!-- more -->

---

## 前言

因为看过CSAPP这本书，关于C语言的函数栈已经了解的比较透彻。实际上JavaScript的函数调用栈也是类似的。

![](stackframe.png)

再来总结一下：
- 一个函数被调用时，就会构造这个函数调用所需要的栈帧
- 栈帧内包含这个函数所使用的局部变量和函数调用结束后返回的地址。
- 函数调用结束后，该函数的栈帧会被弹出，即一些局部变量会失效。


## JavaScript的函数调用栈（执行上下文）

一些关键点：

1. JavaScript引擎是一个单线程解释器（是指针对单个JavaScript引擎线程来说，比如单个浏览器窗口），还包含堆和唯一的函数栈。
2. JavaScript代码从上向下执行（当然是同步代码），同一时刻只有一个函数在执行。
3. JavaScript异步代码（asynchronized code）的回调函数最终也会被时间循环加入到这个函数栈中来执行。

## 总结

关键的就是这么多，如果你想深入了解函数栈的机制，不仅仅是JavaScript，那推荐你去做CSAPP的`Bomb Lab`和`Attack Lab`。关键字搜“cmu cs213 csapp lab”。

我已经完成了这两个实验，[链接](http://blog.scarboroughcoral.top/tags/CSAPP-Lab/)