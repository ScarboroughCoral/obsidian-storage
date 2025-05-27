---
title: JavaScript必知33个概念系列：执行上下文（栈帧）、作用域、闭包、this之间的关系
mathjax: false
copyright: true
comment: true
date: 2019-11-17 17:12:35
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
执行上下文（栈帧）、作用域、闭包、this之间的关系。
{% endnote %}

<!-- more -->

---

## 前言

上一篇文章[《JavaScript必知33个概念系列：作用域》](js-concept-7-scope.html)总结的作用域问题，但是有一些关系还是没有整理清楚，本篇文章单独说明执行上下文（栈帧）、作用域、闭包、this之间的关系。

## 执行上下文

执行上下文大体可以分为两种：
- 全局执行上下文
- 函数执行上下文

每个执行上下文有两个状态：
- 创建状态
- 执行状态

### 全局执行上下文

#### 创建状态

- 创建一个global（window）对象
- 创建this变量，默认指向window
- 为全局执行上下文的变量分配内存
- 为全局执行上下文内的变量赋值为`undefined`
- 为全局执行上下文的函数分配内存，并将内容存入内存。

#### 执行状态

- 逐行执行

### 函数执行上下文

#### 创建状态

- 创建一个argarguments（参数列表）对象
- 创建this变量，默认指向window
- 为当前函数执行上下文内的变量分配内存
- 为当前函数执行上下文内的变量赋值为`undefined`
- 为当前函数执行上下文的函数分配内存，并将内容存入内存。


#### 执行状态

- 逐行执行

## 作用域
> MDN 中定义作用域就是当前的执行上下文。

如果在当前函数执行上下文下访问一个变量val，发现当前函数执行上下文内没有，那就向上层执行上下文内找。这条链就称为作用域链。

## 闭包

一个常见的例子

```javascript
var count = 0

function makeAdder(x) {
  return function inner (y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
count += add5(2)
```

利用执行上下文来理解，当调用`makeAdder(5)`之后该函数的执行上下文（包括参数`x`）已经弹出了，但是函数`add5`调用时可以访问`x`，这是因为`makeAdder(5)`返回后创建了闭包，其中保存了`makeAdder`的执行上下文环境，当调用`add5`时，会在闭包环境下创建自己的执行上下文，那么变量就可以沿着作用域链访问了（就可以访问`x`）。


## this

`this`存在于当前执行上下文中，其引用会因绑定方式而决定，请看[JavaScript 必知 33 个概念系列：this、call、bind、apply](http://localhost:4000js-concept-15-this-call-bind-apply.html)。

箭头函数创建执行上下文时不会创建`this`变量，所以内部访问变量时会沿着作用域链向上找。

## Reference

- [The Ultimate Guide to Hoisting, Scopes, and Closures in JavaScript](https://tylermcginnis.com/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)