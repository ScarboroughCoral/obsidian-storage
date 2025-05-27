---
title: JavaScript必知33个概念系列：原始数据类型和对象属性【WIP】
mathjax: false
copyright: true
comment: true
date: 2019-11-24 18:36:50
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
number、boolean、null、undefined、string、bigint、symbol
{% endnote %}

<!-- more -->

---

## 前言

这篇文章会总结一下JS的原始类型和对象属性相关的问题。关于类型隐式转换会在后续文章中总结。

本篇文章大致会涉及以下内容：
- 各种原始数据类型介绍
- 原始数据类型的自动装箱和拆箱
- 对象数据属性和对象访问属性
- 对象属性描述符


## 原始数据类型

根据[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Primitive_values)的说法，ECMAScript标准定义了8种数据类型：

- Seven data types that are primitives:
  - Boolean
  - Null
  - Undefined
  - Number
  - BigInt
  - String
  - Symbol
- and Object

### Number Type&BigInt

之前写过一篇文章，是关于JS的number类型的，JS中没有Integer，只有double-float（虽然关于位运算提供了一层32位抽象），关于IEEE754标准已经写过几篇文章了，这篇文章不会再提及。有兴趣的可以看一下这两篇文章：

- [IEEE754 实例 ——JS 中的 Number 类型](/ieee754-float-instance-js.html)
- [有关计算机浮点数的思考。](/float-think.html)

接下来总结一些常用点：

```javascript
NaN!==NaN//true，规定
NaN===NaN//false
1>NaN//false
1<=NaN//false
```

### Boolean
> `true` or `false`

```javascript
Boolean(false)//false
Boolean("false")//true
Boolean(new Boolean(false))//true
Boolean(true)//true
Boolean({})//true
Boolean(undefined)//false
Boolean(null)//false
Boolean([])//true
```

### Null
> `null`

一般把`null`考虑为一个对象引用，这个对象引用为空。

```javascript
typeof null//object
```

### Undefined
> `undefined`

一切声明但未初始化的变量值为`undefined`，对于`var`声明的变量仅在执行上下文创建时期引起的变量提升初值为`undefined`

### String
> immutable,too。不像C语言那样可以改。

```javascript
let test="abc";
test[0]='d';
test//abc
```

### Symbol

> 独一无二，不可修改，可用作对象属性的key


## Wrapper & Auto-Boxing

原始类型没有对象类型的优点，比如内置属性方法什么的，所以就有了一些原始类型的封装类，称之为`Wrapper Object`。

比如这些：
- String
- Boolean
- Number
- ...

就以`String`函数举例，使用它有两种方式：

- 以普通函数方式，将入参转换为字符串并返回对应String的原始类型
  ```javascript
  String(1337); // "1337"
  String(true); // "true"
  String(null); // "null"
  String(undefined); // "undefined"
  String(); // ""
  String("dog") === "dog" // true
  typeof String("dog"); // "string"
  ```
- 以构造函数方式，创建一个对应类型的封装对象`wrapper object`
  ```javascript
  const pet = new String("dog")
  typeof pet; // "object"
  pet === "dog"; // false
  //pet是一个对象
  /*
  {
    0: "d",
    1: "o",
    2: "g",
    length: 3
  }
  */
  ```
  

## Data property & accessor property

## Property Descriptor

## Reference

- [(Not) Everything in JavaScript is an Object](http://blog.brew.com.hk/not-everything-in-javascript-is-an-object/)
- [The Secret Life of JavaScript Primitives](https://javascriptweblog.wordpress.com/2010/09/27/the-secret-life-of-javascript-primitives/)
- [Diving Deeper in JavaScripts Objects](https://blog.bitsrc.io/diving-deeper-in-javascripts-objects-318b1e13dc12)
- [The differences between Object.freeze() vs Const in JavaScript](https://medium.com/@bolajiayodejithe-differences-between-object-freeze-vs-const-in-javascript-4eacea534d7c)