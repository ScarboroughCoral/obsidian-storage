---
title: 用JavaScript实现一个任务队列
mathjax: false
copyright: true
comment: true
date: 2019-11-18 20:10:52
tags:
- JavaScript
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
用JavaScript实现一个任务队列。
{% endnote %}

<!-- more -->

---

## 前言

头条一面的题，有思路，但是JavaScript Promise用的不够熟练，记录一下。


## 题目

给定一个API，实现对应的功能。

### API

给定如下接口，请实现一个任务队列。效果是`task`方法用于注册任务，`start`函数调用后，1s后打印1，再过2s后打印2，再过3秒后打印3。

```javascript
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

### 思路

#### 0x1
首先`task`方法仅仅是注册，因此需要将注册的内容保存下来。

定义一个如下对象用于保存注册的任务：

```javascript
this.tasks = [{
    delay:1,
    callback:()=>{},
}];
```

#### 0x2
然后可以链式调用`task`方法，因此其返回值就是`this`。

```javascript
task(delay, callback) {
    this.tasks.push({ delay, callback });
    // console.log(callback)
    return this;
}
```

#### 0x3

最后是关键部分，如何把任务给连接起来并开启队列执行。

执行顺序应该是这样的

```bash
task1设置
->1s...task1运行
->task2在task1回调函数执行后设置
->2s...task2运行
->task3在task2回调函数执行后设置
->3s...task3运行
```

我们创建一个`Promise`对象，添加一个定时器，在`setTimeout`回调函数中执行完注册的callback后`resolve`。这样我就可以在这个`Promise`对象的then方法中添加下一个定时器。

### 实现

完整代码如下：

```javascript
class Queue {
    constructor() {
        this.tasks = [];
    }
    task(delay, callback) {
        this.tasks.push({ delay, callback });
        // console.log(callback)
        return this;
    }
    start() {
        if (this.task.length === 0) return;
        let cur = this.tasks.shift();
        let func = new Promise((resolve, reject) => {
            console.log("starting......");

            setTimeout(() => {
                cur.callback();
                resolve();
            }, cur.delay);
        });
        while (this.tasks.length !== 0) {
            const element = this.tasks.shift();

            func = func.then(() => {
                return new Promise((resolve, reject) => {
                    setTimeout(() => {
                        element.callback();
                        resolve();
                    }, element.delay);
                })
            });

        }
        func.then(() => {
            console.log("ending.....");

        })
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

## 总结

`Promise`对象的使用不够熟练，后续会继续深入学习，敬请期待。