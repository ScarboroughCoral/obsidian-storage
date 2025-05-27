---
title: JavaScript必知33个概念系列：this、call、bind、apply
mathjax: false
copyright: true
comment: true
date: 2019-11-16 15:45:00
tags:
- JavaScript
- 33 JS Concept
categories:
- Front-End Development
- JavaScript

---

{% note primary %}
this、call、bind、apply
{% endnote %}

<!-- more -->

---

## 前言

JavaScript的灵活性也导致了规则的多样性。今天来总结一下JavaScript中的`this`关键字，及其相关话题`bind,call,apply`

## JavaScript中的this

以前学其他语言比如说Java或者CPP时，那时理解的`this`就是当前对象，当调用构造函数创建对象时，`this`会绑定到创建的对象上。关于JavaScript中的`this`并没有过多的关注。

### JavaScript中this的5个绑定规则
> 函数在哪里调用，`this`的引用就来自于这里。

- 隐式绑定
- 显示绑定
- new 绑定
- 词法绑定
- window 绑定

接下来我们一一解释。

#### 隐式绑定

如下代码，大多数`this`绑定的情况都是如此，你只需要判断调用函数的对象就知道this指的谁，**即“.”左侧**。下面例子中`this`分别指的是`user`和`user.mother`。
```javascript
const user = {
  name: 'Tyler',
  age: 27,
  greet() {
    alert(`Hello, my name is ${this.name}`);
  },
  mother: {
    name: 'Stacey',
    greet() {
      alert(`Hello, my name is ${this.name}`);
    }
  }
}
user.greet();
user.mother.greet();
```

#### 显示绑定

显示绑定就用到`bind,call,apply`方法了。这三个方法来自于`Function.prototype`。所以说每个函数都会有这三个方法。其中`call,apply`来自于ES3，`bind`来自于ES5。

##### call

> “call” is a method on every function that allows you to invoke the function specifying in what context the function will be invoked.

如下代码，当调用`greet`函数的时候并不知道这个`this`引用的哪个对象，无法通过`user.greet(x,x,x)`来调用，因为`user`并没有这个函数对象。那么我们只能单独的调用这个函数，我们可以通过`call`的方式来指定调用当前函数的对象，第一个参数就是`this`引用的对象，剩余的参数是函数调用所需要的参数。

```javascript
function greet (l1, l2, l3) {
  alert(
    `Hello, my name is ${this.name} and I know ${l1}, ${l2}, and ${l3}`
  )
}

const user = {
  name: 'Tyler',
  age: 27,
}

const languages = ['JavaScript', 'Ruby', 'Python']

greet.call(user, languages[0], languages[1], languages[2])
```

##### apply

`apply`和`call`类似，只不过`apply`解决了`call`一个问题，就像上面那个例子，当调用的函数需要接受很多参数的时候，需要一个一个将参数传入，如果我们有10个参数就需要单独输入10个参数。而`apply`只需要将参数当做数组传入，不必一个一个传入。

```javascript
const languages = ['JavaScript', 'Ruby', 'Python']

// greet.call(user, languages[0], languages[1], languages[2])
greet.apply(user, languages)
```

##### bind

`bind`和`call`也是类似的，只不过他不会立即调用函数，而是返回一个新函数，这个新函数绑定了一系列参数，其中第一个参数就是`this`的引用对象，其余参数是函数调用时所需要的参数，当你调用新函数时，仅需要将未绑定的参数（如果已全部绑定就无需传参）传入就行了。

还是上面的例子

```javascript
function greet (l1, l2, l3) {
  alert(
    `Hello, my name is ${this.name} and I know ${l1}, ${l2}, and ${l3}`
  )
}

const user = {
  name: 'Tyler',
  age: 27,
}

const languages = ['JavaScript', 'Ruby', 'Python']

const newFn = greet.bind(user, languages[0], languages[1], languages[2])
newFn() // alerts "Hello, my name is Tyler and I know JavaScript, Ruby, and Python"
```

#### new 绑定
> 当使用`new`创建对象时，`JavaScript Interpretor`就会创建一个对象，这个对象叫`this`，也就是说`this`的引用对象就是新创建的对象。

如下代码
```javascript
function User (name, age) {
  /*
    Under the hood, JavaScript creates a new object called `this`
    which delegates to the User's prototype on failed lookups. If a
    function is called with the new keyword, then it's this new object
    that interpretor created that the this keyword is referencing.
  */

  this.name = name
  this.age = age
}

const me = new User('Tyler', 27)
```

#### 词法绑定

所谓的词法绑定就是“你主观认为`this`的引用对象就是所绑定的对象”


例子如下，我们使用`reduce`函数，传入一个回调函数，这个回调函数中使用的`this`会引用哪个对象？当我们使用`function`定义时会创建`context`，`this`是引用的这个`context`，就如同下面的代码，这是有问题的。

```javascript
const user = {
  name: 'Tyler',
  age: 27,
  languages: ['JavaScript', 'Ruby', 'Python'],
  greet() {
    const hello = `Hello, my name is ${this.name} and I know`

    const langs = this.languages.reduce(function (str, lang, i) {
      if (i === this.languages.length - 1) {
        return `${str} and ${lang}.`
      }

      return `${str} ${lang},`
    }, "")

    alert(hello + langs)
  }
}
user.greet()//Uncaught TypeError: Cannot read property 'length' of undefined. 
```

**然后，怎么解决？**
答案是使用箭头函数，箭头函数不会为`this`创建`context`，因此会引用外部的`context`，这是一个闭包。下面的代码就能正常工作了。

```javascript
const user = {
  name: 'Tyler',
  age: 27,
  languages: ['JavaScript', 'Ruby', 'Python'],
  greet() {
    const hello = `Hello, my name is ${this.name} and I know`

    const langs = this.languages.reduce((str, lang, i) => {
      if (i === this.languages.length - 1) {
        return `${str} and ${lang}.`
      }

      return `${str} ${lang},`
    }, "")

    alert(hello + langs)
  }
}
```


#### window 绑定

当不满足前几种绑定时，那`this`就会绑定window（用于catch-all）。

如下代码，此情况`this`的引用对象就是window，因为window对象下没有age这个属性，所以undefined。如果定义了这个属性那就会输出这个属性。

```javascript
function sayAge () {
  console.log(`My age is ${this.age}`)
}

const user = {
  name: 'Tyler',
  age: 27
}
sayAge() // My age is undefined

window.age=27
sayAge() // My age is 27
```

当然，如果你不想产生这种效果，不想让`this`绑定到window对象，那么你可以使用`use strict`声明来避免，window会认为`this`是undefined，如同下面的代码：

```javascript
'use strict'

window.age = 27

function sayAge () {
  console.log(`My age is ${this.age}`)
}

sayAge() // TypeError: Cannot read property 'age' of undefined
```


#### 判断套路

1. 检查函数要在哪里被调用
2. `this`是否在箭头函数内部，如果是，那么`this`有可能词法绑定，引用父亲作用域的`this`（产生了闭包
3. 函数是否使用了`new`创建对象，如果有，那么`this`引用这个创建的对象。
4. 函数是否使用了`call,apply,bind`，如果有，那么`this`引用显示绑定的对象。
5. 函数左侧有无“.”，如果有，那么`this`就引用这个对象。
6. 你是否使用了`use strict`，如果有，那么`this`是`undefined`
7. `this`引用了`window`对象

## 相关话题

### 箭头函数的this
箭头函数没有在函数调用创建函数执行上下文的时候创建`this`对象。详情请看[JavaScript 必知 33 个概念系列：执行上下文（栈帧）、作用域、闭包、this 之间的关系](js-concept-7-scope-2.html)

## Reference

- [Understanding the "this" keyword, call, apply, and bind in JavaScript](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/)