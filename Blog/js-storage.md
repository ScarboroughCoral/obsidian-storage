---
title: Web API系列：存储
mathjax: false
copyright: true
comment: true
date: 2019-11-17 14:17:16
tags:
- Web API
categories:
- Front-End Development
- Web API

---

{% note primary %}
cookie、localStorage、sessionStorage、indexedDB
{% endnote %}

<!-- more -->

---

## 前言

之前可能用过`Web API`对象里面的存储对象，比如说`cookie`和`localStorage`。但是仅仅把他们当做一个浏览器端存储的工具，并没有认真区分它们的不同和应用场景。

首先为什么需要浏览器端存储数据的`Web API`？HTTP是一个无状态（stateless）协议，需要浏览器记录一些东西。

**本篇主要是简单的了解一些概念和用法，不会深入的总结**，后续会深入了解。

今天总结的主要有三个话题：
- 区别
- 使用场景举例
- 使用方法（Web API）

## 区别

### localStorage

- 存储的数据没有截止日期（有效期）的设置，也就是说只要你不主动删除它，它就可能永远存在。
- 只能由客户端读取
- 只能通过JavaScript清除存储条目，或者是用浏览器清除缓存和本地存储数据。
- 存储的限制容量比sessionStorage和cookie要大


### sessionStorage

- 存储的数据只在一个会话中有效，也就是说当关闭浏览器（或者对应tab）时数据会失效。
- 数据不会自动提交到服务器，只能由客户端读取
- 存储的限制容量大于cookie（至少5MB）


### cookie

- 一般存储一些后续请求中需要发送给服务器的数据，也就是说每次请求都会发送cookie（会涉及性能问题）
- 有效期会可以由客户端设置，也可以由服务端设置（一般有服务端设置）
- Cookie一般主要由服务端读取，当然客户端也可以读取。（localStorage和sessionStorage只能由客户端读取）
- 存储大小小于4KB
- 可以设置`httpOnly`为`true`，这样就禁止客户端读取cookie
- 涉及隐私问题

### indexedDB

- 浏览器客户端的低级API
- 能够存储大量的结构化数据，包括文件，支持大量数据存储。
- 使用索引进行查询，性能高
- 是一个事务型数据库系统，有点像基于SQL的关系型数据库管理系统（但列不是固定的）
- 是基于JavaScript的面向对象数据库

## 使用场景举例

### localStorage

- 持久的较少量数据存储

### sessionStorage

- 会话相关的较少量数据存储


### cookie

Cookie的工作过程是这样的：
1. 客户端发起请求
2. 服务端想要在客户端保存一些状态信息，于是在HTTP响应报文添加头部字段`Set-Cookie`，这样客户端就会保存下来。
   1. 可以指定有效时间（`Expires`或`Max-Age`）和域名路径（`Domain`和`Path`）来限制cookie。
   2. `Secure`对于`http:`协议尽管加密也不安全，一些浏览器已经已经不支持不安全的网站（`http:`）设置`Secure`了。
   3. `HttpOnly`设置之后不允许浏览器端JavaScript访问
   ```bash
    HTTP/2.0 200 OK
    Content-type: text/html
    Set-Cookie: yummy_cookie=choco
    Set-Cookie: tasty_cookie=strawberry

    [page content]

   ```
3. 客户端的后续请求都会携带客户端的cookie信息，即请求头`Cookie`字段
   ```bash
   GET /sample_page.html HTTP/2.0
   Host: www.example.org
   Cookie: yummy_cookie=choco; tasty_cookie=strawberry
   ```

下面是一些cookie的使用场景。

- 会话管理
  - 登陆状态、购物车、游戏分数等服务端需要记录的
- 个性化
  - 用户偏好、主题、其他设置等
- 追踪
  - 记录分析用户行为

### indexedDB

- 大量数据存储
- 高性能查询

## 使用方法

### localStorage

- set
  ```javascript
    localStorage.setItem('myCat', 'Tom');
    localStorage.setItem('colorSetting', '#a4509b');
    localStorage.colorSetting = '#a4509b';//不推荐
    localStorage['colorSetting'] = '#a4509b';///不推荐
  ```
- get
  ```javascript
    var cat = localStorage.getItem('myCat');
  ```
- remove
  ```javascript
    localStorage.removeItem('myCat');
  ```
- clear
  ```javascript
    // Clear all items
    localStorage.clear();
  ```

### sessionStorage

和localStorage接口类似。

### cookie

- write
  ```javascript
    document.cookie = "yummy_cookie=choco"; 
    document.cookie = "tasty_cookie=strawberry"; 
  ```
- read
  ```javascript
    console.log(document.cookie); 
    // logs "yummy_cookie=choco; tasty_cookie=strawberry"
  ```
- reset
  ```javascript
    document.cookie = "yummy_cookie="; 
    document.cookie = "tasty_cookie="; 
  ```

### indexedDB

先了解一下indexedDB的推荐使用流程

1. 检查是否支持indexedDB
2. 打开数据库`database`
3. 在数据库中创建一个对象存储仓库`object store`
4. 开启事务，请求数据库操作（添加、获取等）
5. 等待操作完成（可以通过监听事件来操作）
6. 使用查询结果

其他的使用方法暂时不深入了解了，以后用到时再写一篇。

## 相关话题

## Reference

- [Local Storage vs Session Storage vs Cookie](https://scotch.io/@PratyushB/local-storage-vs-session-storage-vs-cookie)
- [HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- [IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
- [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
- [Using IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB)