---
title: CSS系列：对齐
mathjax: false
copyright: true
comment: true
date: 2019-11-18 13:56:37
tags:
- CSS
categories:
- Front-End Development
- CSS

---

{% note primary %}
水平居中、垂直居中、右对齐。
{% endnote %}

<!-- more -->

---

## 前言

元素居中是实际前端开发中常用的一种操作。元素居中分为垂直居中和水平居中，为了以后方便重查，本篇文章总结一下一些常见的对齐方式。

**样例代码如下，查看时请参考下面的代码。**


<p class="codepen" data-height="800" data-theme-id="dark" data-default-tab="css,result" data-user="scarboroughcoral" data-slug-hash="oNNmbMV" style="height: 400px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Centering with CSS Part 1">
  <span>See the Pen <a href="https://codepen.io/scarboroughcoral/pen/oNNmbMV">
  Centering with CSS Part 1</a> by 李明岳 (<a href="https://codepen.io/scarboroughcoral">@scarboroughcoral</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 水平居中

#### 文本水平居中

- `text-align`属性

#### div水平居中

- **蓝色盒子**，将父容器设为`text-align:center`，div设为`display:inline-block`，适用于多个div水平居中。
- 【推荐】**黄色盒子**，使用`margin:auto`属性设置（需要设置宽度）
- 【推荐，常用于响应式开发，注意脱离文档流带来的问题】**绿色盒子**，父容器`position:relative`，div设置`position:absolute`和`left:50%`和`transform:translateX(-50%)`
- **绿色盒子**，父容器`position:relative`，div设置`position:absolute`和`left:50%`和`margin-left:(-width/2)`
- 【极度推荐,响应式，无需计算margin】**粉色盒子**，父容器`display:flex`并`flex-direction:row`（默认），然后`justify-content:center`（要看主轴的方向）

### 右对齐

#### 文本右对齐

- 使用`text-align:right`

#### div右对齐

- 使用`position:absolute`和`right:0`实现
- 【推荐】使用`flex`布局
- （十分奇妙）使用`margin-left:auto`，需要设置宽度
- 使用`float`，注意脱离文档流带来的问题（eg：如果高度大于父容器，则需要设置父容器`overflow:auto`）
- 将父容器设为`text-align:right`，div设为`display:inline-block`


### 垂直居中

垂直居中不太好演示，代码直接在这里写了。

#### 文本垂直居中

- 《不推荐》使用`padding`
- 【推荐】使用`height`和`line-height`设置，考虑文本多行
```css
.center {
  line-height: 200px;
  height: 200px;
  border: 3px solid green;
  text-align: center;
}

/* If the text has multiple lines, add the following: */
.center p {
  line-height: 1.5;
  display: inline-block;
  vertical-align: middle;
}
  ```

#### div垂直居中

- 父容器设置`line-height`和`height`等高，div设置为`display:inline-block`和`vertical-align:middle`
```css
.parent{
  height:200px;
  line-height:200px;
}
.child{
  display:inline-block;
  vertical-align:middle;
}
```
- 父容器`position:relative`，div设置`position:absolute`和`top:50%`和`margin-top:(-height/2)`
- 【推荐,响应式】父容器`position:relative`，div设置`position:absolute`和`top:50%`和`transform:translateY(-50%)`
```css
.parent{
  position:relative;
}
.child{
  position:absolute;
  top:50%;
  transform:translateY(-50%);
}
```

- 【极度推荐,响应式，无需计算margin】使用`flex`
```css
.parent{
  display:flex;
  align-items:center;
}
.child{
  width:100px;
  height:100px;
}
```

## 总结

大致就是如此，后续发现其他方法继续补充。


## Reference

- [CSS Layout - Horizontal & Vertical Align](https://www.w3schools.com/css/css_align.asp)
- [How to center things with style in CSS](https://www.freecodecamp.org/news/how-to-center-things-with-style-in-css-dc87b7542689)