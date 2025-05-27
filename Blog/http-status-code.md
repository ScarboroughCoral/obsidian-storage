---
title: 协议系列：HTTP常见请求方式和状态码总结
mathjax: false
copyright: true
comment: true
date: 2019-11-20 15:37:45
tags:
- Protocol
- HTTP
categories:
- Protocol
- HTTP

---

{% note primary %}
404 not found。
{% endnote %}

<!-- more -->

---

## 前言

最近在看[同源策略和跨域问题](secure-same-origin-policy.html)时，看到CORS的解决方案是需要prefight，跨域时先向目标源发送一个OPTIONS请求，就发现这部分知识没有系统的了解过。今天就来总结一下常见的HTTP请求方式和HTTP状态码。

- [前言](#%e5%89%8d%e8%a8%80)
- [常见HTTP请求方式](#%e5%b8%b8%e8%a7%81http%e8%af%b7%e6%b1%82%e6%96%b9%e5%bc%8f)
- [常见HTTP状态码](#%e5%b8%b8%e8%a7%81http%e7%8a%b6%e6%80%81%e7%a0%81)
  - [1xx informational response，信息响应](#1xx-informational-response%e4%bf%a1%e6%81%af%e5%93%8d%e5%ba%94)
  - [2xx success，成功](#2xx-success%e6%88%90%e5%8a%9f)
  - [3xx redirection，重定位](#3xx-redirection%e9%87%8d%e5%ae%9a%e4%bd%8d)
  - [4xx client errors，客户端错误](#4xx-client-errors%e5%ae%a2%e6%88%b7%e7%ab%af%e9%94%99%e8%af%af)
  - [5xx server errors，服务器错误](#5xx-server-errors%e6%9c%8d%e5%8a%a1%e5%99%a8%e9%94%99%e8%af%af)
- [总结](#%e6%80%bb%e7%bb%93)
- [Reference](#reference)


## 常见HTTP请求方式

这些请求方式是case-sensitive的，但是一些请求工具或请求框架可能已经做了toUpperCase处理。

- GET（HTTP1.0）。这个是最常见的请求方式了，只读资源而不产生副作用。
- POST（HTTP1.0）。请求数据通常来自于表单。
- OPTIONS（HTTP1.1）。请求返回服务器对特定资源支持哪些请求方式。
- HEAD（HTTP1.0）。和Get类似之只读资源并不产生副作用。区别是响应无响应体，只想获取一些响应头中的元信息。
- PUT（HTTP1.1）。指定URI，如果对应资源存在则修改（替代）；如果不存在则创建。不带验证机制。
- DELETE（HTTP1.1）。删除指定资源。不带验证机制。
- TRACE（HTTP1.1）。使用 SSL（Secure Sockets Layer，安全套接层）和 TLS（Transport Layer Security，传输层安全）协议把通信内容加密后经网络隧道传输。
- CONNECT（HTTP1.1）。服务器会将通信路径返回给客户端。
- PATCH。对资源进行部分修改，区别于PUT。不带验证机制。

## 常见HTTP状态码

- 1xx informational response，信息响应。接收的请求正在处理。
- 2xx success，成功。请求正常处理完毕。
- 3xx redirection，重定位。需要进行附加操作以完成请求。
- 4xx client errors，客户端错误
- 5xx server errors，服务器错误

### 1xx informational response，信息响应

### 2xx success，成功

### 3xx redirection，重定位

- 304 Not modified。请求方通过`If-Modified-Since`或者`If-None-Match`请求头知道请求的资源并未改变，服务器无需重传，客户端使用缓存的版本即可。

### 4xx client errors，客户端错误

- 400 Bad Request。客户端错误导致服务器无法处理，比如请求格式错误、数据太大等。
- 401 Unauthorized。类似403。
- 403 Forbidden。客户端请求时包含非法数据，服务器理解请求但是拒绝处理。可能是因为用户无权限，或者请求了一个禁止的行为（比如创建重复记录）。
- 404 Not Found。资源未找到。
- 405 Method Not Allowed。请求方法不允许。比如使用PUT方法访问只读资源，应该使用POST而使用GET。
- 415 Unsupported Media Type。媒体文件格式不支持。比如客户端上传图片使用`images/svg+xml`，而服务器需要另外的图片格式。

### 5xx server errors，服务器错误

- 500 Internal Server Error。服务器内部错误，需要查看服务器报错日志。
- 502 Bad Gateway。请求的服务器是作为一个网关或者代理运行的。上流服务器返回的响应是非法的。
- 503 Service Unavailable ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。

## 总结

这部分内容只是简单地总结。一些状态码和方法并没有实际的用过，因此用于回查。


## Reference

- [List of HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
- [Hypertext Transfer Protocol](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
- [CyC2018 HTTP](https://github.com/CyC2018/CS-Notes/blob/master/notes/HTTP.md)