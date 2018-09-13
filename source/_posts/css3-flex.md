---
title: css3之flex
categories:
  - coding
  - css
tags:
  - css
date: 2017-11-21 15:00:25
update: 2017-11-22 14:57:30
---

css布局方式主要有下面几种方式：
1. 标准文档流
2. 浮动布局
3. 定位布局
4. flex布局（display: flex）

在这里记录下`flex布局`的一些用法。

<!--more-->

`flex`的核心的概念就是**容器**和**轴**。容器包括外层的**父容器**和内层的**子容器**，轴包括**主轴**和**交叉轴**，
可以说`flex`布局的全部特性都构建在这两个概念上。`flex布局`涉及到 12 个 CSS 属性（不含 `display: flex`），
其中父容器、子容器各 6 个。不过常用的属性只有 4 个，父容器、子容器各 2 个。

![flex](/images/css/flex.png)

flex布局的全部属性，一共 12 个，父容器、子容器各 6 个，可以随时通过下图进行回顾。

![flex-attr](/images/css/flex_attr.png)

### 父容器

#### justify-content

用来调整**子元素**水平排列方式，主要有以下参数：
- `flex-start`：子元素靠左排列
- `flex-end`：子元素靠右排列
- `center`：子元素居中排列
- `space-between`：子元素之间有相等的空格; 一行内所有的空格在元素之间平分
- `space-around`：更多的空格；一行内所有的空格在元素前后（即`2*n`）平分
- `space-evenly`：较少的空格

#### align-items

用来调整**子元素**垂直方向排列方式，主要有以下参数：
- `flex-start`：子元素靠顶部排序
- `flex-end`：子元素在底部排序
- `center`：子元素居中排序
- `baseline`：在容器的baseline位置排序
- `stretch`：自动延伸填充容器

#### align-content

用来设置每行之间相隔多少间隔，主要有以下参数：
- `flex-start`：行堆满在顶部
- `flex-end`：行堆满底部
- `center`：行堆在中间
- `space-between`：每行有一定的间隔
- `space-around`： 每行都包裹着一定的间隔
- `stretch`：适应整个容器

#### flex-direction

用来调整**子元素**的**排序**，主要有以下参数：
- `row`：子元素按照文本顺序，**从左到右**排序
- `row-reverse`：子元素按照文本顺序**反序**排序
- `column`：子元素**从上到下**排列
- `column-reverse`：**从上到下反序**排序

**Note**: 当`flex-direction: column`时，`justify-content`变成调整**垂直方向**，`align-items`调整**水平方向**

#### flex-wrap

用来调整**子元素**自动换行，主要有以下参数：
- `nowrap`：所有子元素都在一行里面
- `wrap`：自动换行
- `wrap-reverse`：反序自动换行

#### flex-flow

因为`flex-direction`和`flex-wrap`这两个属性在使用经常一起使用，所以就有了结合两个属性功能的新属性
`flex-flow`。使用时用空格把设值分开，例如`flex-flow: row wrap`>


### 子容器

#### order

用来调整**自身**的排列顺序。默认元素的`order`是`0`，数字越大越靠后，数字越小越靠前，数字相同就正常排序。

**Note**: 必须父类设置`display: flex`

#### align-self

用来设置**自身**的垂直位置，参数和`align-items`相同。

**Note**: 必须父类设置`display: flex`

#### flex-basis

用来设置**自身元素**基准大小：表示在不伸缩的情况下子容器的原始尺寸。主轴为横向时代表宽度，主轴为纵向时代表高度。

#### flex-grow

用来设置**自身元素**弹性伸展的比例。

**Note**：弹性伸展是指把**剩余的空间**按比例分配给对应的元素。

#### flex-shrink

用来设置**自身元素**弹性收缩的比例。

**Note**：弹性伸展是指把**超出的部分**按比例从对应的元素中减去。

#### flex

控制元素在主轴上如何伸缩。`flex`是三个属性的缩写：`flex-glow`，`flex-shrink`，`flex-basis`。

当`flex`只有一个值时，如果这个值没有单位，那这个值指向`flex-glow`属性。如果有单位，那这个值指向`flex-basis`。
当`flex`有两个值时，第一个值指向`flex-glow`，第二个值根据是否有单位指向`flex-shrink`和`flex-basis`。
当`flex`有三个值时，分别表示`flex-glow`，`flex-shrink`，`flex-basis`。


## 参考
1. [FLEXBOX FROGGY](http://flexboxfroggy.com/)
2. [一劳永逸的搞定 flex 布局](https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb)
