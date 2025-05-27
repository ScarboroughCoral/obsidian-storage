---
title: rCore Lab 第二期：批处理系统
date: 2022-07-05 02:09:20
tags:
- Operating System
- Rust
---

{% note primary %}
如何用 `Rust` 从零实现一个批处理系统。
{% endnote %}

<!-- more -->

---

## 前言

这篇文章主要梳理一下关于 rCore 第二章批处理系统的思路。

1. 整体架构
2. 各个架构详细描述
3. 理解难点梳理

## 整体架构

对于应用层的软件来说，可以把操作系统当做一个服务，也可以称之为 Runtime，或者是文档中提到的运行环境（Environment Execution）

引用文档中的一张图片，参考这张图就很容易理解了：
![](rCore-2/execution-environment.png)
> 图片来源： https://rcore-os.github.io/rCore-Tutorial-Book-v3/chapter2/1rv-privilege.html

下层给上层提供运行环境，即功能和接口，上层调用接口获得能力。
整体来看系统分为三层：最上层的应用软件、操作系统（Supervisor）、操作系统Runtime（硬件+简单软件）。操作系统通过ABI即systemcall（系统调用）为应用软件提供能力支持，操作系统Runtime（在riscv中被称为SBI）通过SBI为


## 总结

## Reference

- https://learningos.github.io/rust-based-os-comp2022/chapter2/