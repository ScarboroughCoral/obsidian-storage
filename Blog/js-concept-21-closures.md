---
title: JavaScript必知33个概念系列：闭包
mathjax: false
copyright: true
comment: true
date: 2019-11-15 16:18:57
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
闭包是函数及其创建时保存的状态环境。
{% endnote %}

<!-- more -->

---


## 前言

JavaScript中的闭包是区别于离散数学中的闭包。让我们仔细探讨一下JavaScript中的闭包吧。

## 闭包

### 什么是闭包
> A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function’s scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.(MDN)

也就是说，闭包是函数及其创建时保存的引用状态环境。

### 用闭包模拟私有方法

例如**Java**允许定义私有方法，这样只允许类内的其他方法使用，无法通过类的实例访问。
有如下代码
```java
class Counter{
    private int privateCounter=0;
    private void changeBy(int val){
        this.privateCounter+=val;
    }
    public void increment(){
        changeBy(1);
    }
    public void decrement(){
        changeBy(-1);
    }
    public int value(){
        return privateCounter;
    }
}
```

我们可以使用JavaScript来实现。

```javascript
var makeCounter = function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  };
};
var counter = makeCounter();
console.log(counter.value()); // logs 0
counter.increment();
counter.increment();
console.log(counter.value()); // logs 2
counter.decrement();
console.log(counter.value()); // logs 1
```




### 闭包作用域链

关于作用域链可以查看以前的文章：[JavaScript必知33个概念系列：作用域](http://blog.scarboroughcoral.topjs-concept-7-scope.html)

每个闭包的作用域分为三个：
- 闭包自己的作用域
- 外部函数作用域
- 全局作用域

```javascript
// global scope
var e = 10;
function sum(a){
  return function(b){
    return function(c){
      // outer functions scope
      return function(d){
        // local scope
        return a + b + c + d + e;
      }
    }
  }
}

console.log(sum(1)(2)(3)(4)); // log 20
```

每个函数都能访问外部函数作用域和全局作用域，即每个函数都能够访问作用域链上游作用域的全部变量。

### 闭包的典型错误问题：循环创建闭包

如下代码：

```html
<p id="help">Helpful notes will appear here</p>
<p>E-mail: <input type="text" id="email" name="email"></p>
<p>Name: <input type="text" id="name" name="name"></p>
<p>Age: <input type="text" id="age" name="age"></p>
```

```javascript
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    }
  }
}

setupHelp();
```

以上执行结果就是总是弹出最后一项内容提示，因为`var`是函数作用域的，所有创建的闭包都是引用的同一个对象item。

**解决办法就是使用`let`或者是立即执行函数，`foreach`迭代也可以**

### 闭包性能问题

- 不必要的闭包会影响脚本效率，包括处理速度和内存消耗。

比较下面两组代码：

```javascript text1
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
  this.getName = function() {
    return this.name;
  };

  this.getMessage = function() {
    return this.message;
  };
}
```

```javascript text2
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}
MyObject.prototype.getName = function() {
  return this.name;
};
MyObject.prototype.getMessage = function() {
  return this.message;
};
```

text1在构造函数内部创建了闭包，而且每次创建一个对象都会创建一个函数对象 。
text2使用原型定义函数，不同的对象只有一个函数对象副本。


## 总结
还有一些深入话题没有总结，目前水平还不够，to be continued。


## Reference

- [Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)