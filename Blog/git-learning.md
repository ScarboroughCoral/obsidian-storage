---
title: 《Git三剑客》学习笔记：基础
mathjax: false
copyright: true
comment: true
date: 2020-02-22 13:48:48
tags:
- Git
categories:
- Tools
- Git

---

{% note primary %}
Git 基础和基本命令。
{% endnote %}

<!-- more -->

---

## 前言

是时候系统地学一下git了。这篇文章主要了解git的基本知识及基础命令。

## 基本知识

### 暂存区

workspace->stage->local storage

### `.git`目录

- `HEAD`文件是一个引用，引用当前工作的分支（即指代当前工作分支的最新commit。或者是引用任意commit，此时应该是head detach分离头指针状态）。**常用用法可以使用HEAD进行指代commit**
- `config`文件保存一些配置。比如`git config --local user.name 'lmy'`会保存到`config`文件。
- `refs`文件夹下回保存一些引用id，一般包含`heads`文件夹和`tags`文件夹
  - `heads`文件夹保存的是分支引用文件，每个文件保存着目标分支的最新commit id
  - `tags`文件夹保存的是历史标签引用文件，每个文件保存着目标标签commit的引用
- `objects`文件夹保存一些`git object`，里面的文件夹名和内部文件名共同组成一个哈希值。其中常见有如下三个对象：
  - `commit`对象
  - `blob`对象
  - `tree`对象

#### `commit`,`blob`,`tree`之间的关系

- 每个commit会保存一些信息，其中包含一个tree字段，这实际上保存的是当前commit整个项目的一个快照。你可以使用`git cat-file -p commitId`来查看commit的内容。
  ```bash
    $ git cat-file -p ee41693d74c26fff545574804aa716148d90d2fb
    tree 63f224293af826081ddb830cb7818b4291ef7f50 #指向项目根目录
    parent abb8e578c1ceba076fa23fdfdc9ae2f302ae2877 #父commit
    author lmy <lmy@lmy.lmy> 1582360504 +0800 #作者
    committer lmy <lmy@lmy.lmy> 1582360504 +0800 #提交者

    Modify style.scss

  ```
- 每个tree可以看做一个树，他可以有叶子节点即blob，也可以有子树（另一个tree）。
- blob就是当前commit下的当前文件的快照。

## 基础篇常用命令

### `gitk`

一个查看历史日志的强大工具。可以想象为基于`git log`和`git grep`的图形工具。

### `git gui`

相比于`gitk`，`git gui`是偏向于创建commit的辅助工具

### `git checkout`

- `git checkout -b branchName commitId`可以由commitId所在的commit创建一个名为branchName的分支且切换到此。

### `git add`

- `-u`参数可以一次性提交已经被add的文件到暂存区。

### `git mv`

- `git mv 1 2`命令可以直接重命名马上commit而不需要add（比重命名之后再添加简单，因为那样认为你是删除了原文件添加了新文件）

### `git log`

- `--oneline`参数可以让每个commit只展现一行
- `--all`参数可以显示所有分支的所有commit，不仅仅包含当前分支
- `--graph`参数可以以图的方式展现包含commit的父子关系
- `--n[x]`参数可以显示时间上最近的x个commit日志

### `git diff`

- `git diff commitId1 commitId2`比较两个commit的差异
- HEAD指代，比如`git diff HEAD HEAD^`（当前commit和父commit比较），`git diff HEAD HEAD^^`（当前commit和祖父commit比较）。以上两个命令也可以分别用`git diff HEAD HEAD~1`和`git diff HEAD HEAD~2`来比较

## 场景

### HEAD Detach（分离头指针）
> 切换分支时切换到某个commit而不是某个分支。

- 好处
你可以利用分离头指针做一些测试commit，可以切换回原来的分支来舍弃这些测试commit

- 坏处
容易不小心舍弃一些commit，一般在操作时会有warning。
  - 如果不小心切换到head detach的状态，可以使用`git checkout -b new-name`来保存这些测试。
  - 如果不小心切换到head detach状态并且提交commit后又切换到其他分支，可以使用`git branch branch-name commitId`来保存

## 总结

- HEAD指向当前commit
- 分支实际上指向当前分支的最新commit
- 标签就是给某个commit做了一个标记
- commit中有一个tree属性指向项目根目录。
- tree可以看做是文件夹，文件夹中可以包含其他文件夹（tree），也可以包含文件（blob）
- `.git/config`文件保存了一些局部配置（`git config --local xxx xxx`）

## Reference

- 苏玲，《Git三剑客》