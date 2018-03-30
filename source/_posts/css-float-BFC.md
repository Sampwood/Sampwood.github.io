---
title: css之float&BFC
categories:
  - coding
  - css
tags:
  - css
date: 2018-03-28 22:07:29
update: 2018-03-28 22:07:29
---

### float

虽然说浮动和绝对定位都是脱离文档流的布局且父元素获取不到元素高度，但浮动还是和绝对定位有一定的区别的。
绝对定位是**绝对**地脱离文档流，不会对正常文档流产生任何影响。
而浮动则会在文档流中占据一定的位置。这个从文字环绕float图片就可以看出来。
当然，浮动最开始的意图就是为了能让文字环绕图片。

说到浮动，就不得不说下`inline boxes`和`line boxes`的概念。
视觉格式化模型(Visual formatting model)下，文档中每个元素在页面布局中都会产生一个对应的盒子（box）。
块级元素对应`block box`，行级元素对应`inline boxes`，整行的元素会对应一个`line boxes`。
对于块级元素下面直接写的文字，称之为`匿名inline boxes`。

正常情况下`inline boxes`的基线是对齐的，所以只有一行文字会和图片处在同一行内。`line box`的高度由行内最高的`inline boxes`决定。
但是增加`float`属性之后，破坏了元素的`inline boxes`，使其脱离了其原来所在的`line box`链，跟随自身的方位属性，靠边排列。（有点类似于`display: inline-block`）
但浮动元素依旧在文档流中，同处于文档流中的文字实体不会与浮动元素重叠，就出现了文字环绕显示。

### BFC

BFC(block formatting context)，块格式化上下文，是Web页面中盒模型布局的CSS渲染模式。它的定位体系属于常规文档流。

在css的渲染原理中，BFC主要有下面几种生成途径：
1. 根节点（html结点）
2. `float`不为`none`的元素
3. `overflow`不为visible的元素
4. `display`为`table-cell`, `table-caption`, `inline-block`, `flex`, 或者 `inline-flex`中的其中一个。
5. `position`为`absolute`的元素

BFC主要有下面几个特点：
1. BFC内部的排版不会和外面的元素相互影响
2. 当BFC内部又产生BFC的时候，外面的BFC不会包含内部BFC的子元素
3. BFC会计算浮动元素的高宽
4. 在BFC中，每个盒子的左外边框都紧挨着包含快的左边框（从右到左的格式，则紧挨着右边框），除非盒子内部创建了一个新的BFC
5. 同一个BFC中存在外边距折叠，即`margin-top`和`margin-bottom`的重合；不同的BFC则不存在外边距折叠