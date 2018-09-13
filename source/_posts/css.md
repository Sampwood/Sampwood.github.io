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
.parent {
    display: table-cell;
    vertical-align: middle;  //针对自己的居中，而不是子元素  
}
2.
.parent {
    display: flex;
    align-items: center;
}
3.
.parent {
    position: relative/absolute;
}
.child {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
}
```

#### display: table-cell

这个属性的主要作用有两个：
1. 元素垂直居中，看上面的代码；
2. 设置了`display: table-cell`的同一行列表元素都等高，所以可以用来实现等高布局；
3. 当`display: table-cell`的元素设置的宽度大于实际可占用的宽度时，会使用可用宽度来渲染元素，所以可以用在侧边栏的自适应布局上；

#### display: table-row

实现动态高度的页脚（页眉）贴在页面底部（顶部）：
```css
.main {
    height: 100%;
}
.footer {
    display: table-row;
    height:1px;
}
```

#### display: table

可以用来实现动态水平居中：
```css
.child {
    display: table;
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

### 元素尺寸的相关概念

1. 元素尺寸：对应jQuery中的$().width()和$().height()方法，包括padding和border，也就是元素的border box的尺寸。在原生的DOM API中写作offsetWidth和offsetHeight，所以，有时候也称为“元素偏移尺寸”。
2. 元素内部尺寸：对应jQuery中的$().innerWidth和$().innerHeight()方法，表示元素的内部区域尺寸，包括padding但不包括border，也就是元素的padding box的尺寸。在原生的DOM API中写作clientWidth和clientHeight，所以，有时候也称为“元素可视尺寸”
3. 元素外部尺寸：对应jQuery中的$().outerWidth(true)和$().outerHeight(true)方法，表示元素的外部尺寸，不仅包括padding和border，还包括maring，也就是元素的margin box的尺寸。没有相对应的原生的DOM API。

## 参考
1. [css Table布局-display:table](http://www.css88.com/archives/6308)
