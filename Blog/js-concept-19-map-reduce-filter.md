---
title: JavaScript必知33个概念系列：map、reduce、filter
mathjax: false
copyright: true
comment: true
date: 2019-11-22 17:28:58
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
map、reduce、filter
{% endnote %}

<!-- more -->

---

## 前言

终于来到这个主题了。这三个函数可以让你coding像飘柔般流畅。这是一种函数式编程。今天就来总结一下这三种函数的使用方法和使用场景。



## map

MDN的syntax：

```javascript
var new_array = arr.map(function callback(currentValue[, index[, array]]) {
    // Return element for new_array
}[, thisArg])
```

- callback：回调函数
  - currentValue：迭代的当前值
  - 【optional】index：数组当前下标
  - 【optional】array：数组对象
- 【optional】thisArg：执行callback时this的引用对象


### 使用场景

定义一个对应关系，将原集合一一映射到新的集合。

例如，矩阵的数乘：

```javascript
let arr=[1,2,-4,9];
let vectorMultWithNum=(arr,num)=>arr.map(x=>x*num);
let result=vectorMultWithNum(arr,4);
console.log(result)
//[4, 8, -16, 36]
```

## filter

MND的syntax：

```javascript
var newArray = arr.filter(callback(element[, index[, array]])[, thisArg]);
```


- callback：回调函数
  - element：迭代的当前值
  - 【optional】index：数组当前下标
  - 【optional】array：数组对象
- 【optional】thisArg：执行callback时this的引用对象


### 使用场景

定义一个过滤条件函数，满足时返回true，否则false。

例如，获得数组的所有偶数：

```javascript
let arr=[1,3,5,7,9,10];
let result=arr.filter(x=>x%2===0);
console.log(result);
//[10]
```


## reduce



MDN的syntax：

```javascript
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
```

- callback：回调函数
  - accumulator：累加器，或者是上次合并后的结果。或者是initialValue
  - currentValue：迭代的当前值
  - 【optional】index：数组当前下标。注意是否提供了initialValue
  - 【optional】array：数组对象
- 【optional】initialValue：累加器的初始值。如果不指定初始值那就将数组第一个元素值当做初始值并跳过第一个元素。空数组不指定初始值会报错。

### 使用场景

这三个函数中，reduce是最有艺术感的，他能把数组中的所有元素值进行一个一个合并生成最后的结果。

#### 简单sum求和

```javascript
let arr=[1,2,3,4,5];
let result=arr.reduce((sum,cur)=>sum+cur);
console.log(result);
//15
```

#### 多次复合函数

```javascript
let x=1;
const compose = (x,...fns)=>fns.reduceRight((f,g)=>g(f),x);
const fn1=x=>x**2;
const fn2=x=>x+11;
let result=compose(x,fn1,fn2);
console.log(result);
//(x+11)**2===(1+11)**2===144
//144
```

### Promise Chain

之前写过一篇关于使用JavaScript实现一个任务队列的。

如果使用reduce的话会显得代码很简洁。

```javascript
class Queue {
    constructor() {
        this.tasks = [];
    }
    task(delay, callback) {
        this.tasks.push({ delay, callback });
        return this;
    }
    start() {
        if (this.tasks.length === 0) return;
        this.tasks.reduce((s,x)=>{
            return s.then(r=>{
                return new Promise((resolve,reject)=>{
                    setTimeout(()=>{
                        x.callback();
                        resolve();
                    },x.delay);
                });
            });
        },Promise.resolve()).then(x=>{
            this.tasks=[];
            return;
        });
    }

}

new Queue()
    .task(1000, () => {
        console.log(1);
    })
    .task(2000, () => {
        console.log(2);
    })
    .task(3000, () => {
        console.log(3);
    })
    .start();



```

### 其他应用场景

其他场景还有很多，关键点就是迭代合并操作。

## 总结

一些用法还不是很熟练，比如promise，接下来会加强这一部分的连接。再见！

## Reference

- [JavaScript Functional Programming — map, filter and reduce](https://medium.com/jsguru/javascript-functional-programming-map-filter-and-reduce-846ff9ba492d)
