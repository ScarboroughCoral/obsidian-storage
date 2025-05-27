---
title: CSS系列：回流和重绘
mathjax: false
copyright: true
comment: true
date: 2019-12-03 18:53:38
tags:
- CSS
categories:
- Front-End Development
- CSS

---

{% note primary %}
Reflow and Repaint
{% endnote %}

<!-- more -->

---

## 前言

头条面试题，这方面没有深入了解，今天总结一下。

## 浏览器渲染机制

![](http://www.phpied.com/files/reflow/render.png)

- 浏览器将下载好的HTML源码解析成DOM树，根节点是`<html>`（`documentElement`）
- 浏览器解析CSS，忽略掉无法识别的CSS代码，CSS代码包括浏览器默认样式、开发者的CSS文件还有嵌入HTML的style属性里的样式。
- 构造渲染树（`render tree`）。渲染树不同于DOM树，它能够获取到节点样式，仅包含需要渲染的节点，任何不可见的元素（`display:none`，`<head>`）都不会出现在渲染树中。另外如果一个`<p>`标签内有多行元素，在渲染树中每行都会看做是一个节点。
- 渲染树构造完成后浏览器就可以在屏幕上绘制界面了。


## 渲染机制例子

- HTML源码
  ```html
  <html>
    <head>
    <title>Beautiful page</title>
    </head>
    <body>
        
    <p>
        Once upon a time there was 
        a looong paragraph...
    </p>
    
    <div style="display: none">
        Secret message
    </div>
    
    <div><img src="..." /></div>
    ...
    
    </body>
  </html>
  ```
- DOM树
  ```text
  documentElement (html)
    head
        title
    body
        p
            [text node]
		
        div 
            [text node]
		
        div
            img
		
        ...
  ```
- 渲染树
  ```text
  root (RenderView)
    body
        p
            line 1
            line 2
            line 3
            ...
            
        div
            img
	    
	...
  ```

## 回流和重绘（`reflow&repaint`）

首先可以确定的是网页肯定至少有一次布局和绘制，除非是一个空白页面。

### 什么是回流和重绘

- 部分或全部渲染树需要重新验证，节点大小需要重新计算，这称之为**回流**（reflow,relayout）。至少有一次回流发生，即页面的初始布局。
- 页面部分因为样式的改变而需要更新修改结果，比如说背景颜色、字体颜色、可见性（`visibility:hidden`），页面可见的更新称之为**重绘**。

### 什么会触发回流和重绘

- 增加、删除、修改节点
- 隐藏DOM节点：使用`display:none`（回流并重绘）；使用`visibility:hidden`（仅重绘，因为没有布局大小上的改变）
- 移动等动画作用于DOM节点
- 添加样式表，并修改样式
- 用户操作比如放缩窗口大小、修改字体大小、滑动滚动条。

例子：

```javascript
var bstyle = document.body.style; // cache
 
bstyle.padding = "20px"; // reflow, repaint
bstyle.border = "10px solid red"; // another reflow and a repaint
 
bstyle.color = "blue"; // repaint only, no dimensions changed
bstyle.backgroundColor = "#fad"; // repaint
 
bstyle.fontSize = "2em"; // reflow, repaint
 
// new DOM element - reflow, repaint
document.body.appendChild(document.createTextNode('dude!'));
```

### 如何减少回流和重绘

因为除了一些必要的回流和重绘，其他的回流和重绘是不必要甚至是非常浪费资源的，因此我们要尽量避免这种回流和重绘，那么如何避免呢？

#### 浏览器的自动优化

浏览器为回流和重绘设置一个队列，队列里面保存需要修改的内容，浏览器可以批量处理。时间达到限制或者改变数目达到限制，多次引起回流的改变可以绑定成一个，这样只有一次回流。

但有一些改变或请求可以**强制浏览器清空队列并执行重绘**，如下：

- `offsetTop`,`offsetLeft`,`offsetWidth`,`offsetHeight`（只读，当前元素和最近relative祖先元素的left距离，当前元素的可见高度）
- `scrollTop`,`scrollLeft`,`scrollWidth`,`scrollHeight`
- `clientTop`,`clientLeft`,`clienWidth`,`clientHeight`
- `getComputedStyle()`,或者`currentStyle` in IE

因为浏览器必须返回给你这些变量的**最新的值**，因此浏览器必须清空修改队列，并执行相应的更新操作。


#### 常用避免策略

- 不要独立地单独修改样式，修改`class`或者修改`cssText`属性
  ```javascript
    // bad
    var left = 10,
        top = 10;
    el.style.left = left + "px";
    el.style.top  = top  + "px";
    
    // better 
    el.className += " theclassname";
    
    // or when top and left are calculated dynamically...
    
    // better
    el.style.cssText += "; left: " + left + "px; top: " + top + "px;";
  ```
- 不要在动态的DOM树上批量修改
  - 用一个临时进行修改，最终将修改的变量加入到DOM树中。
  - 克隆准备修改的节点，在克隆节点上修改，然后替换原来的节点
  - 隐藏原来的节点（`display:none`，1次回流，1次重绘），在这个节点上进行批量修改，重新显示这个节点（1次回流，1次重绘）。
- 不要频繁计算样式，不要让浏览器的队列频繁刷新执行修改，比如不要在循环中使用上面提到的那些变量。**应该使用一个缓存变量暂时存储。**
  ```javascript
    // no-no!
    for(big; loop; here) {
        el.style.left = el.offsetLeft + 10 + "px";
        el.style.top  = el.offsetTop  + 10 + "px";
    }
    
    // better
    var left = el.offsetLeft,
        top  = el.offsetTop
        esty = el.style;
    for(big; loop; here) {
        left += 10;
        top  += 10;
        esty.left = left + "px";
        esty.top  = top  + "px";
    }
  ```
- 通常来说，可以考虑当前的修改会让渲染树进行多少计算，然后针对性的进行优化。
  > 比如说将一个准备进行动画的节点设为`postion:absolute`使之成为`<body>`的子节点，这就不会影响大量的其他节点

## 总结

- 渲染树是DOM树的可见部分
- 渲染树的重新计算叫做回流
- 重新计算后将修改绘制在屏幕上叫做重绘
- 回流一定重绘
- 重绘不一定回流
- 需要批量修改样式时使用临时变量，或使目标节点暂时脱离渲染树，或使目标节点减少在渲染树上和其他节点的关系，这样可以一定程度减少回流和重绘

## Reference

- [Rendering: repaint, reflow/relayout, restyle](https://www.phpied.com/rendering-repaint-reflowrelayout-restyle/)
