---
title: 操作系统开发平台
date: 2018-12-09 20:03:25
tags:
- Operating System
- Docker

categories: 
- Computer Science
- Operating System
---

{% note default %}

工欲善其事，必先利其器。

{% endnote %}
<!-- more -->




### 前言

如果你也想体验开发操作系统的话，那么这篇博客会很适合你。当你想要开发操作系统的时候你可能需要这几样东西：

- 模拟器
- 编辑器
- 编译器
- 汇编程序

没错，虚拟模拟器用于运行你写好的镜像。编辑器就是你写代码的地方。刚开始你需要写汇编代码这就需要汇编程序来编辑它。编译器用于编译操作系统开发中主要使用的高级语言程序。

### 工具

因为作者考虑除了作者以外可能还有很多人想要学习操作系统，所以就使用 `docker` 把这些工具打包在一起了，如果想下载就访问文章末尾列出的 `Docker Hub` 中的镜像。所有组件一共`700MB`，你可能看着会有点大，这是因为我们使用了 `linux` 作为底层虚拟机。下面是主要工具：

- ubuntu 底层虚拟机
- qemu 用于模拟实际电脑的模拟器
- vim 编辑器之神
- gcc C语言编译器
- nasm 汇编程序


### 运行效果

![](osdev-platform/osdev.png)


### 有关文档


* [Docker Hub](https://hub.docker.com/r/scarboroughcoral/osdev-ubuntu/)
* [开发参考教程](https://github.com/cfenollosa/os-tutorial)
* [QEMU 使用文档](https://qemu.weilnetz.de/doc/qemu-doc.html)
* [Intel 汇编指令集](http://www.skywind.me/maker/intel.htm)
* [Linux 汇编开发指南（IBM）](https://www.ibm.com/developerworks/cn/linux/l-assembly/index.html)
* [Intel 80386 程序员参考手册](https://wizardforcel.gitbooks.io/intel-80386-ref-manual/content/1.html)
* [Writing a Simple Operating System —from Scratch](http://www.cs.bham.ac.uk/~exr/lectures/opsys/10_11/lectures/os-dev.pdf)