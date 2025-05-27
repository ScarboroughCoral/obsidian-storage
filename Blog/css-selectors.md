---
title: CSS系列：CSS选择器总结【WIP】
mathjax: false
copyright: true
comment: true
date: 2019-11-29 14:41:21
tags:
- CSS
categories:
- Front-End Development
- CSS
---

{% note primary %}
type,universal,attribute,class,id,pseudo-class,pseudo-element,combinator
{% endnote %}

<!-- more -->

---

## 前言

css的选择器一直常用的就几个，也就记住了这几个，初学时系统的学过一遍不过现在也忘得差不多了，今天就来总结一下CSS中到底有哪些选择器。


## 总览

所有的选择器如下，我们接下来会一一介绍其用法和语义，还有他们的优先级。

- 简单选择器
  - 通配选择器
  - 标签（类型）选择器
  - 属性选择器
  - 类选择器
  - ID选择器
  - 伪类选择器
- 伪元素选择器
- 组合选择器

## 命名空间

再具体介绍各类选择器之前，我们必须先来谈个概念——命名空间。**这部分内容没怎么用过，不会详细展开，只会简单总结一下用法。以后用到会继续补充，如果有错请右下角小窗或者文章右上角修改pr**

命名空间可以作用于通配选择器、标签（类型）选择器和属性选择器，目的就是只选择某个或某些命名空间内的元素。
命名空间通常用于处理文档中包含多个命名空间，比如说HTML5文档内联一些SVG、MathML、XML等。

### 规则

- 任何 `@namespace` 规则都必须在所有的 `@charset` 和 `@import` 规则之后, 并且在样式表中，位于其他任何 `style declarations` 之前。

### 语法

```css
/* 默认命名空间 */
@namespace url(XML-namespace-URL);
@namespace "XML-namespace-URL";

/* 命名空间前缀 */
@namespace prefix url(XML-namespace-URL);
@namespace prefix "XML-namespace-URL";

/*使用*/

/*匹配prefix命名空间的selector元素*/
prefix|selector{}
/*匹配所有命名空间下的selector元素*/
*|selector{}
/*匹配默认命名空间下的selector元素*/
selector{}

```

### 例子

```css
@namespace url(http://www.w3.org/1999/xhtml);
@namespace svg url(http://www.w3.org/2000/svg);

/* 匹配所有的XHTML <a> 元素, 因为 XHTML 是默认无前缀命名空间 */
a {}

/* 匹配所有的 SVG <a> 元素 */
svg|a {}

/* 匹配 XHTML 和 SVG <a> 元素 */
*|a {}

```



## 简单选择器及其语义

我们把以下常见的6中选择器称为简单原则器。

### 通配选择器

`*`，选择任意标签元素。

### 标签（类型）选择器

`type`，选择`type`类型的标签。


### 属性选择器

根据标签的属性来选择。

#### 属性存在性和值选择器

- `[att]`，选择含`att`属性的元素，不管属性值是什么。
- `[att=val]`，选择含`att`属性并且属性值为`val`的元素。
- `[att~=val]`，选择满足如下条件的元素：含`att`属性，并且`att`属性值是一个列表，列表中有一个值为`val`。（`val`只是一个值，如果其中含空格或者为空串就不会代表任何值）
- `[att|=val]`，选择包含`att`属性并且属性值是`val`或者以`val`开头并且后跟`-`的元素。

#### 值串匹配属性选择器

- `[att^=val]`，选择含`att`属性并且值以`val`开头的元素，如果`val`为空串则不代表任何值。
- `[att$=val]`，选择含`att`属性并且值以`val`结束的元素，如果`val`为空串则不代表任何值。
- `[att*=val]`，选择含`att`属性并且值包含`val`子串的元素，如果`val`为空串则不代表任何值。


### 类选择器

`.selector`，选择含有`class`属性并且值中包含`selector`值的元素。**类似于`[class~=selector]`属性选择器**。

### ID选择器

`#selector`，选择包含`id`属性并且值为`selector`的元素。

### 伪类选择器

`:selector`，伪类是为了解决使用其他简单选择器无法实现的选择问题。选择通常是基于和DOM树无关的信息。通常以`:`开头。

#### 动态伪类选择器
不会出现在源码和DOM树中。


##### 链接伪类选择器

- `:link`，选择未被访问过的链接元素。
- `:visited`，选择已被访问过的链接元素。

##### 用户动作伪类选择器

有可能同时触发。

- `:hover`，选择被用户选定的（不一定激活元素动作）的元素。
- `:active`，选择被用户激活动作的元素。
- `:focus`，选择被用户聚焦的元素。


#### 目标伪类选择器

- `:target`，和网页内部索引有关，某个URI对应一个`id`，比如当前链接是`http://example.com/html/top.html#section_2`，被这个链接能够索引到`id`为`section_2`的元素，`:target`就是选择当前URI索引的`target`元素，如果页内没有`target`（`id`）就不会被触发。

#### 语言伪类选择器

- `:lang(language)`，

#### UI元素状态伪类选择器

- `:enabled`
- `:disabled`
- `:checked`
- `:interminate`

#### 结构伪类选择器



## 伪元素选择器

## 组合选择器

## 优先级


## 总结

## Reference
- [Selectors Level 3](https://www.w3.org/TR/selectors-3/)
- [CSS Namespaces Module Level 3](https://www.w3.org/TR/2014/REC-css-namespaces-3-20140320/)
