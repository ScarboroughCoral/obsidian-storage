---
title: 恶心心JavaScript系列：节流和防抖
mathjax: false
copyright: true
comment: true
date: 2019-12-17 17:18:57
tags:
- JavaScript
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
Reduce event trigger rate by throttling & debouncing
{% endnote %}

<!-- more -->

---

## 前言

节流和防抖都是为了解决一个问题——事件的触发率。事件的触发率，顾名思义就是单位时间内你的回调函数被调用的次数。这篇文章总结一下节流和防抖的使用场景和两者的定义与区别。


## 事件的触发率

什么时候关注事件的触发率呢？
- 点击一个按钮的时候？这个时候触发率有多高？就算你点击的很快那也是比较小的，不够明显。
- 在输入框内迅速的打字，输入框的改变事件触发率高吗？这个是比较明显的，最常见的一个例子就是在搜索引擎内输入时的自动提示。
- 最明显的是什么事件？什么事件的触发率会很高？比如说`scroll`和`mousemove`事件触发率会非常高！


## 使用场景

如果你关注的某个事件的触发率比较高，那么你应该需要注意这个事件的回调函数。

如果你的回调函数内几乎没做什么事，没有很大的计算，没有涉及很慢的操作（下面会说有哪些操作），那么你可以酌情忽略。

如果某个事件的触发率比较高并且其事件回调函数内涉及如下操作，那么你就**需要考虑回流或者防抖**了。


比如说：

- XHR网络请求
- 引起浏览器的回流或者重绘
- 计算大量数据
- ...

{% note success %}
节流和防抖就是为了减少事件的触发率来节省资源。这两种方式实际上就是在响应用户动作和最佳资源使用上的一个折中。
{% endnote %}





## 节流（throttling）

节流，顾名思义就是限制流速，可以给定一个事件触发率的一个上限值，让频繁的事件触发变的稍慢一些。


### 实现方式

当然这个上限值可以通过多种方式来实现：

- 通过每隔多少时间触发一次来设置
- 通过每隔多少次触发当做一次触发来设置
- 通过某个时间段内只能触发几次来设置

重构项目时为了避免重写大量回调函数，我们可以封装一个wrapper函数，这个函数返回一个新的代理函数，原来的回调函数不变。

```javascript
function throttled(delay,fn){
    let lastInvoke=0;
    return function(...args){
        const now=Date.now();
        if(now-lastInvoke<delay) return;
        lastInvoke=now;
        return fn(...args);
    }
}
```

这个封装实际上就是用到了闭包来保存上次事件成功触发的时间。
你可以通过如下方式使用：

```javascript
//原来的回调函数
const myHandler=(event)=>{
    //do sth.
}
//新生成的代理函数
const tHandler=throttled(200,myHandler);
domNode.addEventListener('mousemove',tHandler);

```

这样事件实际触发的上限值是200ms触发一次，两次触发之间的所有无效触发都被忽略。
当然这个`delay`所设置的时间需要有各种考虑，需要平衡网页的响应时间和流畅程度。**节流对于CSS的`transition`比较有效。**

### 节流的应用场景

需要追踪用户的动作，但是因为事件回调的负担，不能高频率触发事件。比如追踪`scroll`的位置来做出某些响应。

## 防抖（debouncing）

防抖，亦可顾名思义，就是防止抖动，把原来的一些列抖动当做一次平移。也就是说将一系列连续的事件触发结束后的一段时间内没有再触发，那么就真正的触发一次。再生动形象一点，就是等风平浪静之后只触发一次事件。



### 防抖的实现


防抖可以通过JS的计时器`setTimeout()`和`clearTimeout()`来实现。

和节流的实现方式类似，我们用一个wrapper函数来创建一个代理函数，原来未防抖的回调函数不变。

```javascript
function debounced(delay,fn){
    let timeId=null;
    return function(...args){
        if(timeId){
            clearTimeout(timeId);
        }
        timeId=setTimeout(()=>{
            fn(...args);
            timeId=null;
        },delay);
    }
}
```

使用方式和节流创建函数类似，如下：

```javascript
const myHandler=(event)=>{
    //do sth.
};
const dHandler=debounced(200,myHandler);
domNode.addEventListener('input',dHandler);
```


### 防抖的应用场景

防抖主要应用于那些不必时时刻刻追踪用户动作的事件触发。比如最常见的例子，Baidu搜索中用户输入的自动提示（假设不保存在本地，而需要XHR请求），你可以设置用户停止输入200ms后触发一次事件，如果两次用户输入小于200ms则不会触发并且需要重新计时。

## 总结

这些常见的节流和防抖的工具函数在一些常见的工具库中就能够找到，比如说[lodash](https://lodash.com/)，其中就有[throttle](https://lodash.com/docs/4.17.15#throttle)和[debounce](https://lodash.com/docs/4.17.15#debounce)函数。

- 节流就是直接限制触发频率，或指定多长时间触发一次，或指定时间段内触发几次，或多次触发当做一次触发
- 防抖是等风平浪静时再响应触发，风平浪静指的是时间触发后x毫秒后未再次触发。
- 节流和防抖无法解决所有的问题，比如恶心冗杂的代码。因此在想用节流防抖来优化降低事件触发率之前先做好其他优化。
- 节流和防抖对于一些场景下的高频事件触发并不适用，比如说某些滑动组件如果使用节流那就会出bug，用户也就感觉恶心心。

## Reference

- [Throttling and debouncing in JavaScript](https://codeburst.io/throttling-and-debouncing-in-javascript-646d076d0a44)