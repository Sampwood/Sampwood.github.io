---
title: css3学习笔记
categories:
  - coding
  - css
tags:
  - css
date: 2017-11-21 15:00:25
update: 2017-11-22 14:57:30
---
### display: flex

#### justify-content

用来调整**子元素**水平排列方式，主要有以下参数：
- `flex-start`：子元素靠左排列
- `flex-end`：子元素靠右排列
- `center`：子元素居中排列
- `space-between`：子元素之间有相等的空格; 一行内所有的空格在元素之间平分
- `space-around`：更多的空格；一行内所有的空格在元素前后（即`2*n`）平分
- `space-evenly`：较少的空格
<!--more-->

#### align-items

用来调整**子元素**垂直方向排列方式，主要有以下参数：
- `flex-start`：子元素靠顶部排序
- `flex-end`：子元素在底部排序
- `center`：子元素居中排序
- `baseline`：在容器的baseline位置排序
- `stretch`：自动延伸填充容器

#### flex-direction

用来调整**子元素**的**排序**，主要有以下参数：
- `row`：子元素按照文本顺序，**从左到右**排序
- `row-reverse`：子元素按照文本顺序**反序**排序
- `column`：子元素**从上到下**排列
- `column-reverse`：**从上到下反序**排序

**Note**: 当`flex-direction: column`时，`justify-content`变成调整**垂直方向**，`align-items`调整**水平方向**

#### order

用来调整**自身**的排列顺序。默认元素的`order`是`0`，数字越大越靠后，数字越小越靠前，数字相同就正常排序。

**Note**: 必须父类设置`display: flex`

#### align-self

用来设置**自身**的垂直位置，参数和`align-items`相同。

**Note**: 必须父类设置`display: flex`

#### flex-wrap

用来调整**子元素**自动换行，主要有以下参数：
- `nowrap`：所有子元素都在一行里面
- `wrap`：自动换行
- `wrap-reverse`：反序自动换行

#### flex-flow

因为`flex-direction`和`flex-wrap`这两个属性在使用经常一起使用，所以就有了结合两个属性功能的新属性
`flex-flow`。使用时用空格把设值分开，例如`flex-flow: row wrap`>

#### align-content

用来设置每行之间相隔多少间隔，主要有以下参数：
- `flex-start`：行堆满在顶部
- `flex-end`：行堆满底部
- `center`：行堆在中间
- `space-between`：每行有一定的间隔
- `space-around`： 每行都包裹着一定的间隔
- `stretch`：适应整个容器

## 参考
1. [FLEXBOX FROGGY](http://flexboxfroggy.com/)
