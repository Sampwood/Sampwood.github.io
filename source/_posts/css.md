---
title: css
date: 2017-08-22 14:45:09
categories:
- coding
- css
tags: 
- css
---
一些css知识。

### 伪类写在css中的顺序

** :link > :visited > (:focus :hover) > :active > :readonly **

focus和hover可以互换，写在后面的优先级高

### rem、em

rem是相对html中的font-size的大小来计算的。值得注意的是chrome中文版的最小字体是12px，所以如果font-size设置了小于12px的值将不起作用。
<!--more-->
对em来说，如果是算font-size的大小，则用父类的font-size；计算其他大小，如margin，则用本身的font-size。

### 盒子模型

`box-sizing: content-box`: 
1. 元素宽度 = width + padding + border
2. 元素content宽度 = width

`box-sizing: border-box`:
1. 元素宽度 = width
2. 元素content宽度 = width - padding - border

### background设置

有效的设置：
```css
background: rgba(0, 0, 0, .3) url(...)
background: url(...), url(...)
```

无效的设置：
```css
background: rgba(0, 0, 0, .3), rgba(0, 0, 0, .3)
background: rgba(0, 0, 0, .3), url(...)
```

### display

定义垂直居中：
```css
1. 
div {
    display: table-cell;
    vertical-align: middle;  //针对自己的居中，而不是子元素  
}
2.
div {
    display: flex;
    align-items: center;
}
3.
div {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
}
```

### position

#### position = relative相对定位

> 1. 如何定位？
> 每个元素在页面的普通流中会“占有”一个位置，这个位置可以理解为默认值，而相对定位就是将元素偏离元素的默认位置，但普通流中依然保持着原有的默认位置。（在父级节点的content-box区定位，父级节点有文字的话，元素的默认位置则是紧随文字）
> 2. 不会改变行内元素的display属性。
> 3. 并没有脱离普通流，只是视觉上发生的偏移。

#### position = absolute绝对定位

> 1.如何定位浮动？
> 1.1.设置了TRBL
> &nbsp;&nbsp;相对最近的设定了position:relative
> &nbsp;&nbsp;absolute的父（祖先）节点的padding-box的区进行定位（忽略文字），找不到符合条件的父（祖先）节点，则相对浏览器窗口进行定位。
> 1.2.没有设置了TRBL
> &nbsp;&nbsp;则默认浮动，默认浮动在父级节点的content-box区。
> 2.不管是块级元素还是行内元素，应用了position:absolute之后，display为block：
> &nbsp;&nbsp;可以设置width和height
> &nbsp;&nbsp;没有设置的话，width默认为auto
> 3.脱离文档流，容器（父）元素将得不到脱离普通流的子元素高度

### 浮动

** overflow:hidden/scroll/auto能清除浮动 (除了overflow:visible) **

清除浮动的三种方式：
```html
1. 
<div>
    <div style="float: left;">left</div>
    <div style="float: right;">right</div>
    <div sytle="clear: both;"></div>
</div>
2.
<div style="overflow: hidden;">
    <div style="float: left;">left</div>
    <div style="float: right;">right</div>
</div>
3.
<style>
.parent:after {
    content: '';
    display: block;
    clear: both;
}
</style>
<div class="parent">
    <div style="float: left;">left</div>
    <div style="float: right;">right</div>
</div>
```
