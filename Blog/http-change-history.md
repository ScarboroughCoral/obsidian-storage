---
title: 协议系列：HTTP历史版本特性更改关键点总结
mathjax: false
copyright: true
comment: true
date: 2019-11-22 17:02:00
tags:
- Protocol
- HTTP
categories:
- Protocol
- HTTP

---

{% note primary %}
HTTP1.0、HTTP1.0、HTTP2.0、HTTP3
{% endnote %}

<!-- more -->

---


## 前言

最近在维基百科看HTTP状态码的时候看到了HTTP的版本历史，发现了一些不知道的东西，今天就来总结一下这部分内容。

一些详细的历史内容比如说谁发布的标准等等，在本篇文章中并不会涉及，读者有兴趣可以自行Google，本篇文章重点是HTTP版本的关键特性变化。

如果有关键内容没有提及或者说有错误的话，请右下方小窗我或者文章右上角修改文件在GitHub发起pr。

- [前言](#%e5%89%8d%e8%a8%80)
- [HTTP1.1](#http11)
- [HTTP2](#http2)
- [HTTP3](#http3)
- [HTTPS](#https)
- [总结](#%e6%80%bb%e7%bb%93)
- [Reference](#reference)


## HTTP1.1

相较于HTTP1.0发生了如下改变：

- 持久连接。HTTP1.0每次TCP连接只允许一次资源请求，一个request——response的pair。HTTP1.1允许每次连接都有多个请求响应pair。这样也利用了TCP的慢启动策略，速度上有明显的提升。
- pipelining流水线。HTTP1.1发送请求后不再等待响应后再请求其他资源，允许一次发送多个请求。
- Byte Range Serving。顾名思义，HTTP1.1允许客户端请求资源的一部分。这个和Chunked transfer encoding可以互相替代。
- Chunked transfer encoding，一种数据流机制。HTTP1.1允许将数据分割成块，这些块独立的进行传输。HTTP2已经不用这种方法了。



## HTTP2

向前兼容，HTTP3没有隔离以前的重要版本，能够运行基于HTTP1.0和1.1的应用程序。

- 不支持HTTP1.1的Chunked transfer encoding，自己提供了另一种类似的数据流机制。

## HTTP3

向前兼容的，HTTP3没有隔离以前的重要版本，能够运行基于HTTP2的应用程序。今年一些浏览器比如Google、Firefox已经开始支持HTTP3了。

- 传输层级基于UDP


## HTTPS



## 总结

暂时就总结这么多，以后遇到时再来添加。

## Reference

- [Hypertext Transfer Protocol](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)