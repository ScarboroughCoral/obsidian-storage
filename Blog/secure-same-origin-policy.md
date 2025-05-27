---
title: Web安全系列：同源策略和跨域请求
mathjax: false
copyright: true
comment: true
date: 2019-11-19 22:52:41
tags:
- Security
categories:
- Security

---

{% note primary %}
同源策略和跨域请求。
{% endnote %}

<!-- more -->

---

## 0x1. 前言

同源策略问题也是一个面试常问的问题，这篇文章来总结一下。

大体内容如下：

- [0x1. 前言](#0x1-%e5%89%8d%e8%a8%80)
- [0x2. 同源策略](#0x2-%e5%90%8c%e6%ba%90%e7%ad%96%e7%95%a5)
  - [0x2.1 什么是同源](#0x21-%e4%bb%80%e4%b9%88%e6%98%af%e5%90%8c%e6%ba%90)
  - [0x2.2 同源问题举例](#0x22-%e5%90%8c%e6%ba%90%e9%97%ae%e9%a2%98%e4%b8%be%e4%be%8b)
  - [0x2.3 同源策略的限制](#0x23-%e5%90%8c%e6%ba%90%e7%ad%96%e7%95%a5%e7%9a%84%e9%99%90%e5%88%b6)
  - [0x2.4 为什么有同源策略](#0x24-%e4%b8%ba%e4%bb%80%e4%b9%88%e6%9c%89%e5%90%8c%e6%ba%90%e7%ad%96%e7%95%a5)
- [0x3. 跨域问题](#0x3-%e8%b7%a8%e5%9f%9f%e9%97%ae%e9%a2%98)
  - [0x3.1 如何发起Ajax跨域请求](#0x31-%e5%a6%82%e4%bd%95%e5%8f%91%e8%b5%b7ajax%e8%b7%a8%e5%9f%9f%e8%af%b7%e6%b1%82)
    - [0x3.1.1 JSONP(JSON with Padding)](#0x311-jsonpjson-with-padding)
    - [0x3.1.2 CORS(Cross-Origin Resource Sharing)](#0x312-corscross-origin-resource-sharing)
    - [0x3.1.3 WebSockets](#0x313-websockets)
    - [0x3.1.4 CORS vs JSONP](#0x314-cors-vs-jsonp)
  - [0x3.2 其他跨域问题](#0x32-%e5%85%b6%e4%bb%96%e8%b7%a8%e5%9f%9f%e9%97%ae%e9%a2%98)
    - [0x3.2.1 跨域共享Cookie](#0x321-%e8%b7%a8%e5%9f%9f%e5%85%b1%e4%ba%abcookie)
    - [0x3.2.2 跨域iframe](#0x322-%e8%b7%a8%e5%9f%9fiframe)
    - [0x3.2.2 跨域localStorage](#0x322-%e8%b7%a8%e5%9f%9flocalstorage)
- [0x4. Reference](#0x4-reference)


## 0x2. 同源策略
> 同源策略是一种安全机制，这种安全机制限制了一个源内的文档或脚本如何和其他源的资源交互。这种机制有利于隔离潜在的恶意文本。

### 0x2.1 什么是同源

如果两个源的【协议、主机、端口号】三元组都相同的话那么就称这两个源是同源的。

### 0x2.2 同源问题举例


举例来说，`http://www.example.com/dir/page.html`这个网址，协议是`http://`，域名是`www.example.com`，端口是`80`（默认端口可以省略）。它的同源情况如下。

- `http://www.example.com/dir2/other.html`：同源
- `http://example.com/dir/other.html`：不同源（域名不同）
- `http://v2.www.example.com/dir/other.html`：不同源（域名不同）
- `http://www.example.com:81/dir/other.html`：不同源（端口不同）


### 0x2.3 同源策略的限制

同源策略限制的是JavaScript的脚本，也就是说一些资源比如说CSS、HTML和动态加载的脚本就能够通过HTML标签跨域。[CSRF](secure-xss-csrf.html)就是利用了这一点。


那么具体限制JavaScript的哪些行为呢？

- 跨域Cookie、LocalStorage 和 IndexDB 无法读取。
- 跨域DOM 无法获得。
- 跨域AJAX 请求不能发送。

### 0x2.4 为什么有同源策略

同源策略在一定程度上保证了信息安全。
就像CSRF那个例子一样。现在网站很多都是通过cookie进行身份认证的，如果网站A的cookie能够在网站B获取，那么我就可以在网站B发送请求给网站A的服务器。


## 0x3. 跨域问题

在一些场景中同源策略就显得限制性太强了，比如不同子域名属于跨域，不允许其交互，但是属于同一个父域名。


### 0x3.1 如何发起Ajax跨域请求

下面是一些常见的Ajax跨域请求方法。


#### 0x3.1.1 JSONP(JSON with Padding)

跨域Ajax请求的常用方法。只能发送`Get`请求。
通过动态添加`<script></script>`标签，利用`src`字段向目的源服务器请求数据，这是不受同源策略限制的，因为并没有使用JavaScript进行跨域请求。

`src`中必须声明回调函数，也就是有一个查询字符串`callback`参数，值为回调函数的名称。

如下：

```html
<script src="http://server.example.com/Users/1234?callback=parseResponse"></script>
```

服务端会将返回的数据包装在`parseResponse()`的括号内（这就是JSONP名称的由来`Padding or Payload`），注意这是一个文本。

客户端实际接收到的数据是`parseResponse(data)`，因为`<scirpt>`标签加载的文件会自动执行，所以只要定义了`parseResponse`这个函数那就会自动调用。

只能通过`<script>`的`src`指定请求路径，这就决定了`JSONP`只能发起get请求。

#### 0x3.1.2 CORS(Cross-Origin Resource Sharing)

目标源服务端允许的源可向其发起跨域请求。

过程如下：

1. 客户端跨域请求
2. 浏览器发送一个`OPTIONS`请求到目标源服务器，这个请求报文里包含`Origin`字段（当前网站的源），还有一些其他细节。
3. 目标源服务端返回。
4. 浏览器检查目标源服务器返回的报文，如果包含`Access-Control-Allow-Origin`字段且当前源满足条件则发起请求。

验证是否满足跨域条件的例子（来自维基百科）：

- `OPTIONS`请求：
```text
OPTIONS /
Host: service.example.com
Origin: http://www.example.com
```
- 目标源服务器返回：
```text
Access-Control-Allow-Origin: http://www.example.com
Access-Control-Allow-Methods: PUT, DELETE
```

维基百科跨域XHR的创建：

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Flowchart_showing_Simple_and_Preflight_XHR.svg/770px-Flowchart_showing_Simple_and_Preflight_XHR.svg.png)


#### 0x3.1.3 WebSockets

`ws`是一种协议，使用`ws://`（非加密）和`wss://`（加密）作为协议前缀，这个协议不受同源策略限制。

客户端请求时添加`origin`字段，表示当前源是哪，服务器根据这个字段进行相应，如果在白名单内则允许本次通信。

#### 0x3.1.4 CORS vs JSONP

- JSONP只支持GET请求；CORS也支持其他HTTP请求
- CORS使程序员写跨域请求像普通请求一样，便于维护；JSONP则相对不易维护。
- JSONP在古老的浏览器上也能运行；CORS在大多数现代浏览器上能运行
- JSONP可能会被XSS攻击如果跨域请求目的源不够安全的话；CORS则更安全。

### 0x3.2 其他跨域问题

#### 0x3.2.1 跨域共享Cookie

跨域Cookie的共享可以通过修改`document.domain`属性来跨域。**`domain`必须是当前页面的域名或者是其父域名。**，而且端口号必须相同，设置`domain`时会将端口号设为`null`。

共享Cookie也可以在服务端设置Cookie时，将Cookie的域名范围设为父域名。

比如`http://a.example.com`和`http://b.example.com`，服务器设置cookie时，只需要设置为`Set-Cookie: key=value; domain=.example.com; path=/`



#### 0x3.2.2 跨域iframe

如果两个不同源的网页拥有共同的一级域名，那么就可以通过修改`document.domain`的方式实现跨域。

如果完全不同源可以通过如下三种方式：

- 片段标识符
- window.name
- Cross-document messaging

前两种比较捞就不总结了，第三种跨文本通信才是正统的Web API，就是通过`window.postMessage(message,origin)`方法进行跨窗口通信，不管是否同源。

一般来说通过postMessage+listener的方式进行通信。

#### 0x3.2.2 跨域localStorage

可以使用如上所说的跨文本通信进行通信。




## 0x4. Reference

- [浏览器同源政策及其规避方法](https://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
- [Same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
- [Same-origin policy for file: URIs](https://developer.mozilla.org/en-US/docs/Archive/Misc_top_level/Same-origin_policy_for_file:_URIs)
- [Same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy)
- [Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)
- [JSONP](https://en.wikipedia.org/wiki/JSONP)