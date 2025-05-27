---
title: JavaScript必知33个概念系列：原型继承和原型链
mathjax: false
copyright: true
comment: true
date: 2019-11-26 19:37:34
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
prototype inheritance、prototype chain
{% endnote %}

<!-- more -->

---

## 前言

JS中的继承是通过原型来实现的，这一部分内容看过很多次，但是一直没有总结，今天就来总结一下这一部分内容




## 4个问题

首先来看这四个判断，我们的内容会由这四个判断展开。

```javascript
Object instanceof Function//true
Object instanceof Object//true
Function instanceof Object//true
Function instanceof Function//true
```

## 必须明确的几个关键点

- 每个对象都可以访问一个`__proto__`属性，指向创建当前对象的构造函数的`prototype`对象。
- 函数也是对象，可以称之为`function object`，即函数对象。
- 每个函数都有一个`prototype`属性，指向原型对象。
- 每个函数的`prototype`对象里都有一个`constructor`属性指向函数本身。
- 原型链的尽头是`Object.prototype`，他没有`__proto__`属性，实际上有这个属性，但是为`null`


## 一些例子

### 例子1

```javascript
let obj=new Object();
//or
let o={};

obj.__proto__===Object.prototype//true
```

因为`obj`是一个对象，所以它有一个`__proto__`属性，并且它由`Object`构造函数创建，`Object`函数有一个`prototype`属性，因此`obj.__proto__`和`Object.prototype`指向同一个对象。不妨自己动手打印一下，这两个对象是一样的。


### 例子2

```javascript
Object.prototype.constructor === Object//true
```

因为每个函数的`prototype`属性都有一个`constructor`属性指向函数本身，因此`Object.prototype.constructor`指向`Object`函数。


### 例子3

```javascript
Object.__proto__ === Function.prototype//true
```

因为函数也是对象（`function object`），因此它有一个`__proto__`属性，那这个属性指向哪呢？函数对象是由哪个构造函数创建的？很容易猜到，就是`Function`对象。因此`Object.__proto__`指向`Function.prototype`。


### 例子4

```javascript
Function.prototype === Function.__proto__//true
```
还是如此，函数也是对象。`Function`函数有一个`__proto__`属性，那它指向哪呢？因为`Function`也是一个函数，创建函数的很容易想到是`Function`，可能有点奇怪，不过就是如此。

### 例子5

```javascript
Function.prototype.constructor === Function//true
```

每个函数的原型对象都会有一个`constructor`属性指向函数本身，因此`Function.prototype.constructor`就是指向`Function`函数。

### 例子6

```javascript
Function.prototype.__proto__ == Object.prototype//true
```

某个函数的原型对象是一个对象，那么他的`__proto__`属性指向谁呢？因为`Object`函数用于创建对象，因此`Function.prototype.__proto__`就指向`Object.prototype`。



## instanceof怎么判断的以及4个问题的答案

就如下代码，`instanceof`的判断条件是，如果在`b.prototype`在`a`的原型链中就返回`true`，如果没有则`false`。

```javascript
a instanceof b
```

因此一开始的4个问题的答案就很容易解答了。

### 问题1

```javascript
Object instanceof Function//true
```
因为`Object.__proto__`指向`Function.prototype`，即`Function.prototype`在`Object`的原型链上。


### 问题2

```javascript
Object instanceof Object//true
```

因为`Object.__proto__.__proto__`指向`Object.prototype`，即`Object.prototype`在`Object`的原型链上。


### 问题3

```javascript
Function instanceof Function//true
```

因为`Function.__proto__`指向`Function.prototype`，即`Function.prototype`在`Function`的原型链上。

### 问题4

```javascript
Function instanceof Object//true
```

因为`Function.__proto__.__proto__`指向`Object.prototype`，即`Object.prototype`在`Function`的原型链上。



## 如何实现原型继承

总共分3步（这没有实现静态方法的继承）：

1. `super.call(this)`
2. `child.prototype=Object.create(super.prototype)`
3. `child.prototype.constructor=child`

### 例子

```javascript
function Animal(name){
    this.name=name;
}

function Cat(name,color){
    Animal.call(this,name);//#1，构造父类属性
    this.color=color;
}
Cat.prototype=Object.create(Animal.prototype);//#2，实现原型链继承
Cat.prototype.constructor=Cat;//#3，设置构造函数
```

### 静态的继承

在ES6的`class-extends`方式中，你可以指定静态`static`的方法和属性，如下：

```javascript
class Animal{
    constructor(){

    }
    static staticMethod(){
        console.log("static method invoked!");
    }
}

class Dog extends Animal{
    constructor(){
        super();
    }
}
Dog.staticMethod();//static method invoked!
```

因为`Dog`函数本身没有这个方法，那么肯定在它的原型链上，问题是Dog的原型根据ES5的写法应该`Dog.__proto__===Function.prototype`为`true`，然而这个例子中`Dog.__proto__===Animal`为`true`，这说明上面ES5的继承方法缺少了静态继承，**补全以上代码的话应该有4步**：

1. `super.call(this)`
2. `child.prototype=Object.create(super.prototype)`
3. `child.prototype.constructor=child`
4. `child.__proto__=Animal`
```javascript
function Animal(name){
    this.name=name;
}

function Cat(name,color){
    Animal.call(this,name);//#1，构造父类属性
    this.color=color;
}
Cat.prototype=Object.create(Animal.prototype);//#2，实现原型链继承
Cat.prototype.constructor=Cat;//#3，设置构造函数
Cat.__proto__=Animal//#4，实现静态继承
```

## 头条面经的几个问题

在看头条面经时看到的问题，来一一分析一下。

```javascript

Object.prototype.a='Object'
Function.prototype.a = 'Function'
function Person(){}
var child = new Person()
 
console.log(Person.a)
console.log(child.a)
console.log(child.__proto__)
console.log(child.__proto__.__proto__)
console.log(child.__proto__.__proto__.constructor)
console.log(child.__proto__.__proto__.constructor.constructor)
console.log(child.__proto__.__proto__.constructor.constructor.constructor)
```

### 打印1

```javascript
console.log(Person.a)//Function
```

首先`Person`是一个函数对象，它本身没有`a`这个属性，因此需要沿着原型链向上找。函数是由`Function`创建的，因此`Person.__proto__`指向`Function.prototype`，而`Function.prototype`有属性`a`，值为“Function”因此结果为“Function”。

### 打印2

```javascript
console.log(child.a)//Object
```

首先`child`对象本身没有这个属性，因此需要沿着原型链向上找。`child`由`Person`创建，因此`child.__proto__`指向`Person`的原型，`Person.prototype`没有这个属性，因此继续向上找，`Person.prototype`是由`Object`创建的，因此`Person.prototype.__proto__`指向`Object`的原型对象，而`Object.prototype.a`是“Object”，因此结果是“Object”


### 打印3

```javascript
console.log(child.__proto__)//Person.prototype
```

`child`对象由`Person`创建，因此`child.__proto__`指向`Person.prototype`，打印的就是`Person.prototype`，不出意外的话里面有一个`constructor`属性指向`Person`，还有一个`__proto__`属性指向`Object.prototype`。

### 打印4

```javascript
console.log(child.__proto__.__proto__)//Object.prototype
```

参考**打印3**，`child.__proto__`指向`Person.prototype`，因此就等价于打印`Person.prototype.__proto__`，而`Person.prototype`是由`Object`创建的，因此`Person.prototype.__proto__`指向`Object.prototype`因此打印的就是`Object.prototype`里面的内容。


### 打印5

```javascript
console.log(child.__proto__.__proto__.constructor)//Object
```

参考**打印4**，直到`child.__proto__.proto__`指向`Object.prototype`，那它的`constructor`属性就指向`Object`函数本身。


### 打印6

```javascript
console.log(child.__proto__.__proto__.constructor.constructor)//Function
```

参考**打印5**，知道`child.__proto__.__proto__.constructor`指向的是`Object`，那么`Object.constructor`指向的谁呢？首先`Object`本身没有`constructor`属性，那么就沿着原型链向上找，而`Object`是一个函数对象，即由`Function`创建，因此`Object.__proto__`指向`Function.prototype`，而`Function.prototype.constructor`是`Function`本身，因此结果就是“Function”。



### 打印7

```javascript
console.log(child.__proto__.__proto__.constructor.constructor.constructor)//Function
```

参考**打印6**，因为`child.__proto__.__proto__.constructor.constructor`指向`Function`，因此等价于`Function.constructor`，而`Function`函数对象本身没有这个属性，于是沿着原型链向上找，`Function.__proto__`指向`Function.prototype`，而`Function.prototype.constructor`是`Function`本身，因此结果也是“Function”

## 总结


- 每个对象都可以访问一个`__proto__`属性，指向创建当前对象的构造函数的`prototype`对象。
- 函数也是对象，可以称之为`function object`，即函数对象。
- 每个函数都有一个`prototype`属性，指向原型对象。
- 每个函数的`prototype`对象里都有一个`constructor`属性指向函数本身。
- 原型链的尽头是`Object.prototype`，他没有`__proto__`属性，实际上有这个属性，但是为`null`

## Reference

- [Prototype in Javascript](https://www.codementor.io/sandeepranjan2007/prototype-in-javascipt-knbve0lqo)
- [Prototypes in JavaScript](https://medium.com/better-programming/prototypes-in-javascript-5bba2990e04b)
- [北京头条前端面经](https://www.nowcoder.com/discuss/346582?type=0&order=0&pos=15&page=0)
- [Prototype in JavaScript: it’s quirky, but here’s how it works](https://www.freecodecamp.org/news/prototype-in-js-busted-5547ec68872/)