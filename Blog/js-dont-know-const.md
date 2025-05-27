---
title: 恶心心JavaScript系列：对象设置不变性
mathjax: false
copyright: true
comment: true
date: 2019-12-16 14:27:41
tags:
- JavaScript
categories:
- Front-End Development
- JavaScript
---

{% note primary %}
如何让JS中的对象不变。
{% endnote %}

<!-- more -->

---

## 前言

在其他语言比如Java中你可以设置对象为常量，对象属性为常量，通过使用`final`关键字。在JS中你可能知道使用`const`关键字，但是对于一个对象用`const`关键字描述结果是什么？只是将变量所保存的引用索引无法改变了而已，对象内部还是可以改变的。

今天我们就来总结一下有哪些方法可以让一个对象保持不变性。

## 不变性

目前为止，你可能只知道通过`const`来设置一个变量为常量，但是对象如何设置为常量？通过`const`修饰只能保证其“值不变性”，如何保证其“引用目标内容不变性”？这就是我们要讨论的话题。

### const
`const`只是修饰的变量本身，如果该变量是个值类型，那么这个值不可改变；如果该变量是个引用类型，则该变量无法再引用其他引用类型，也就是引用对象目标不可变，但是对象内部可以改变。


```javascript

const a=1;
a=2;//不可改：Uncaught TypeError: Assignment to constant variable.

const arr=[1,2,3];
arr.push(4);//可改，看下一行的打印
console.log(arr);//[1,2,3,4]
arr=[4,5,6];//不可改：Uncaught TypeError: Assignment to constant variable.

const obj={};
obj.m=1;//可改，看下一行打印
console.log(obj);//{m:1}
obj={};//不可改：Uncaught TypeError: Assignment to constant variable.

```

### 属性描述符

如果想让一个对象属性变为真正的常量，可以通过描述符的方式，使其**不可修改、不可重定义或删除**。

```javascript
const obj={};
Object.defineProperty(obj,'m',{
    value:1,
    writable:false,
    configurable:false;
    enumerable:true
})
obj.m=2;
console.log(obj.m);//1
```

### 禁止扩展

通过`Object.preventExtensions()`方法可以禁止一个对象添加新属性。

```javascript
const obj={
    a:1
}
Object.preventExtensions(obj);
obj.b=2;
console.log(obj.b);//undefined
```

### 密封

通过`Object.seal()`方法可以“密封”一个对象，相当于先调用`Object.preventExtensions()`，再将所有现有属性改为`configurable:false`。原来是`writable:true`的仍可修改。
```javascript
const obj={
    a:1
}
Object.seal(obj);
delete obj.a;//false
console.log(obj.a);//1
```

### 冻结

通过`Object.freeze()`方法可以“冻结”一个对象，相当于先调用`Object.seal()`，再将所有属性改为`writable:false`。
```javascript
const obj={
    a:1
}
Object.freeze(obj);
obj.a=2;
console.log(obj.a);//1
```

### 深不可变性

深不可变性可以通过DFS或者BFS，递归或者队列设置不可变性。

## 总结

- 通过描述符可以设置一个对象属性不可变性
- 通过`Object.preventExtensions`设置对象不可添加属性
- 通过`Object.seal`设置对象不可添加属性，不可配置已有属性和删除已有属性
- 通过`Object.freeze`设置对象所有属性“浅不可变”

## Reference

- [You Don't Know JS Yet: Objects & Classes - 2nd Edition->Chapter 3: Objects](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/objects-classes/ch3.md)