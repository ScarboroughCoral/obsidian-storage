---
title: A Tiny Shell——CSAPP之Shell Lab
mathjax: false
copyright: true
comment: true
date: 2019-11-01 17:02:14
tags:
- Operating System
- CSAPP
- CSAPP Lab
categories:
- Computer Science
- Operating System

---

{% note primary %}
利用Linux信号实现一个简单的Shell。
{% endnote %}

<!-- more -->

---


## 前言

很恶心，代码写完了，都过了。不过有的测试用例rtest卡了，认为是修改了，重下一遍，结果不小心覆盖了，git上传也不完整。。靠大脑cache来总结吧

## 信号

信号类似于一种中断，或者可以称之为软件中断。先来总结一下关于异常控制流ECF（Exception Control Flow）的分类吧。

异常控制流，或者称之为异常，这是广义的异常，只要不是正常的逻辑控制流，就是异常。

异常分为同步异常和异步异常。异步异常就是中断（分为硬件中断和软件中断），同步异常就是狭义的异常（包括陷阱trap，错误fault，和终止abort）

### 异常分类
1. 同步的
   1. 陷阱trap，有目的的。eg：system call
   2. 错误fault，无目的的，有可能恢复。eg：pagefault
   3. 终止abort，无目的的，不能恢复。eg：非法指令
2. 异步的
   1. 硬件中断
   2. 软件中断
      1. 信号
      2. ...

## 信号处理执行过程

几个标志字：
pending：delivered信号，等待被处理
blocked：被阻塞的信号，可以加入到pending里，但不会被receive


信号处理分为几个阶段：
1. deliver：放到pending里
2. receive：Handler处理

信号处理过程：
假设内核已经从一个异常处理程序中返回并且要切换到进程P：
1. 内核计算进程P的信号状态：pending_unblocked_signals=pending&~unblocked
2. 如果pending_unblocked_signals为0则直接切换到进程P
3. 如果不为0则就依次强制进程P处理信号，直到为0（信号处理可能被其他信号中断）

##  waitpid (pid_t pid, int *statusp, int options)函数详解
1. pid>0等待一个特定pid的进程，pid=-1，等待所有子进程，（还支持其他类型等待集合，比如Unix进程组，在此不做讨论）
2. status用于保存等待返回进程的状态
3. options有三个。WNOHANG——等待集合中没有进程终止则立即返回，WUNTRACED——挂起调用进程直到等待进程终止或停止，WCONTINUED——挂起调用进程直到等待进程终止或被停止的进程收到SIGCONT重新执行

## 关键点

> 写一下关键的地方吧。按照测试来写，具体能容可能由测试来展开。


### test01

CTRL+D退出，自带

### test02

内置quit命令。
判断使用已定义好的builtin_cmd函数，匹配quit字符串返回1，不是内置则返回0
首先在eval函数里判断，如果是内置指令则单独执行，无需fork。单独执行时直接exit。

### test03

运行一个前台进程。

这说明不是一个内置指令了，那就需要fork和execve

这时候需要注意几点：
1. 因为这时候要addjob，有可能会产生deletejob发生在addjob之前。所以要在exceve之前阻塞SIGCHLD信号来避免此问题发生。
2. 利用parseline函数返回值来判断是前台还是后台，如果是前台，tsh需要挂起（`while(flag) suspend(&mask)`）直到前台程序结束或停止。
3. 判断前台程序结束使用一个标志位，在SIGCHLD Handler里waitpid时判断如果当前进程是fg的话就修改标志位`flag`。此时因为received一个SIGCHLD信号，`suspend()`函数会被触发结束挂起，此时发现循环条件不满足，这就取消挂起了。

### test04
运行一个后台进程。

与前台程序相反：
1. 通过parseline函数返回值判断是bg，则tsh无需挂起
2. 与前台类似，exceve之前也要block SIGCHLD信号，并将job设为BG

### test 05

运行多个后台进程，并使用jobs命令打印当前jobs

1. jobs是个内置命令，在builtin_cmd函数里面匹配jobs字符串调用已经写好的listjobs函数并返回1

### test06

运行一个前台进程，发送一个SIGINT信号给此进程（按下CTRL+C

1. 首先需要tsh进程接受到SIGINT信号。tsh的SIGINT信号的处理程序源文件中已经给注册（signal）了，我们只需要填写完成信号处理函数就OK了
2. tsh接受到SIGINT信号，如果此时有前台进程则发送给前台进程及其后代进程（后续再说其后代进程的处理），发送使用kill函数，pid通过fgpid函数获得
3. 此时前台进程会被SIGINT信号默认结束进程。这会deliver给tsh进程一个SIGCHLD信号，通过`WIFSIGNALED(status)`来判断此进程是否由信号终止的，status有waitpid获取（后续讲），这时就可以打印了，通过`WTERMSIG(status)`来获取引发终止的信号

### test07
确认只发送SIGINT信号给前台程序，因为我们通过fgpid获取的进程id，所以肯定是只发送信号给前台进程了。直接过

### test08
发送一个SIGTSTP信号给前台程序。和SIGINT类似，不过有点区别


1. tsh接受到SIGTSTP信号处理过程是一样的，发送给fgpid获取的进程。
2. SIGTSTP信号被子进程received之后，子进程默认停止。父进程被delivered一个SIGCHLD信号，在SIGCHLD Handler里需要使用`WIFSTOPPED(status)`函数来判断当前进程是否被停止，如果停止则设置state为ST，并打印。此时listjobs则会看到状态发生了改变



### test09

内置`bg %jid(or pid) `命令，作用是在后台运行一个已停止的进程。
和其他内置命令一样，不过需要额外解析jid或者pid，然后需要发送一个SIGCONT信号

1. 关于tsh进程发起`bg %jid`命令，tsh进程需要发送SIGCONT信号给对应的job进程，需要等待job进程接受到SIGCONT信号并开始执行，然后tsh进程SIGCHLD Handler处理。
   > waitpid函数需要添加**WCONTINUED**的选项来拿到此进程。
2. tsh在SIGCHLD Handler里需要将当前进程置为BG

### test10

内置`fg %jid(or pid) `命令，作用是在前台运行一个已停止的进程。

1. 和bg命令类似，需要解析jid和发送SIGCONT信号
2. fg需要将目标进程在前台执行，这就需要tsh进程挂起，和前台进程类似

### test11

发送SIGINT信号给前台进程组里的所有进程。

1. 默认情况下，fork的子进程是和父进程同一个进程组的，进程组由pgid唯一标识
2. 前台进程组是由exceve执行的进程及其后代进程组成的，只需要对exceve进程设置gpid即可，通过`setgpid(pid,pgid)`函数来设置，pid=0则为当前进程设置，pgid=0则使用当前进程pid来作为pgid。我们使用当前进程pid作为pgid，后代进程和此进程是相同的pgid
3. 使用kill(pid,signum)来发送信号，当pid<0时则把pid的绝对值当做pgid，将signum对应的信号发送给pgid的所有进程中

### test12

发送SIGTSTP信号给前台进程组里的所有进程。和test11类似，不赘述

### test13
发送SIGCONT信号给后台pid对应停止进程的进程组里的所有进程。和test11类似，不赘述

### test14
简单错误处理，比如fg一个不存在的进程或进程组，或缺少参数。进行字符串匹配即可，不赘述。

### test15
Putting it all together

这个出了点问题
1. `bg %1`没打印东西，而`jobs`命令后将`bg %1`的东西打印了。解决办法，输出缓冲区的内容没有输出到设备，使用fflush(stdout)函数。
2. tsh进程挂起标志位flag的设置，只需要在waitpid当前进程是FG进程并且停止`WIFSTOPPED(status)==1`或终止`WIFEXITED(status)==1`状态才结束挂起。

### test16

能够处理来自其他进程的信号，毫无疑问可以。

## 总结

OK，结束了。本次实验主要是熟悉了信号的使用，包括如何定义信号处理函数，如何响应子进程状态的改变，如何同步信号引发的一些问题等。另外也大体了解了shell是个什么东西，6.828再见。