---
title: 恶心心JavaScript系列：原型链属性隐藏规则
mathjax: false
copyright: true
comment: true
date: 2019-12-16 10:45:17
tags:
- JavaScript
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
让你误解的JavaScript原型链属性隐藏规则。
{% endnote %}

<!-- more -->

---

## 前言

JavaScript的原型可能是最让你无解的地方，我们总是千方百计地想通过JS来实现继承，但是实际上JS仅仅是对象关联和任务委托罢了。JS中的继承看起来更像兄弟之间的任务委托而不是真正的父子关系。JS中没有构造函数只有构造函数调用。

好了，切入正题。在JS中，你可能认为是这样，你有一个对象`myDog`，它的原型链上其中一个对象有一个`name`属性值为`Tom`，`myDog`并没有这个属性，现在你通过`myDog.name`访问结果一定是`Tom`没错。但是如果你为`myDog`通过`obj.key=value`的形式新添加一个`name`属性，其值为`Jone`，你可能会回答原型链对象上的属性被屏蔽了。

但其实并不是如此。

## 屏蔽规则

这是需要某种规则的，那么这个规则是什么？下面我们一一讨论`obj.key=value`的三种情况。

### 原型链某对象上存在对应属性名并且没有标记为只读，那么就会为当前对象添加属性，原型链对象上的属性被屏蔽

换句话说，原型链某个对象上存在同名属性并且`writable:true`，那么就会屏蔽。**这就是我们认为应该确实如此的规则**

看个例子：

```javascript
function Dog(){}
Object.defineProperty(Dog.prototype,'name',{
    value:'Tom',
    writable:true,
    configurable:true,
    emumerable:true
});
let myDog = new Dog();
console.log(myDog);//"Tom"
myDog.name="Jone"
console.log(myDog);//"Jone"
```

### 原型链某对象上存在对应属性名并且标记为只读，那么就会忽略为当前对象添加属性，严格模式下会出错

话句话说，如果原型链某个对象上存在同名属性且`writable:false`，那么就忽略添加属性操作，严格模式报错。

还是那个例子：

```javascript
function Dog(){}
Object.defineProperty(Dog.prototype,'name',{
    value:'Tom',
    writable:false,
    configurable:true,
    emumerable:true
});
let littleDog = new Dog();
console.log(littleDog.name);//"Tom"
littleDog.name="Jone"
console.log(littleDog.name);//"Tom"
littleDog.hasOwnProperty('name');//false
```

### 原型链某对象上存在对应属性名的setter，那么就会忽略为当前对象添加属性并调用这个setter。

话句话说，原型链的某个对象上存在同名的setter，当你为当前对象同名属性赋值时会调用这个setter。

如上例：

```javascript
function Dog(){}
Object.defineProperty(Dog.prototype,'name',{
    get (){return this._name;},
    set (val){this._name=val},
    configurable:true,
    emumerable:true
})
let tinyDog=new Dog();
tinyDog.name="Tom";
console.log(tinyDog.name);//"Tom"
tinyDog.name="Jone";
console.log(tinyDOg.name);//"Jone"
tinyDog.hasOwnProperty('name');//false
```

## 如何强制屏蔽

针对后两种情况会导致属性无法屏蔽，要想屏蔽可以使用`Object.defineProperty()`方法进行创建属性。


## 总结

使用`obj.key=value`进行属性创建的时候，有可能会屏蔽原型链上的属性也有可能不屏蔽，有如下规则：
1. 原型链上存在同名数据属性并且可写`writable:true`，那么就会在`obj`上创建属性并屏蔽原型链上的属性。
2. 原型链上存在同名数据属性并且不可写`writable:false`，那么就忽略在`obj`上创建同名属性动作，严格模式下报错
3. 原型链上存在同名`setter`函数，那么会调用这个函数。

要想强制屏蔽后两种情况，请用`Object.defineProperty()`方法。

## Reference

- [You Don't Know JS Yet: Objects & Classes - 2nd Edition->Chapter 5: Prototypes](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/objects-classes/ch5.md)