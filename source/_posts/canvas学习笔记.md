---
title: canvas学习笔记
date: 2017-11-14 16:51:03
categories:
- coding
- js
tags: 
- html5 
- canvas
toc: true
---

```
let canvas = document.getElementById('myCanvas');
let ctx = canvas.getContext('2d');

ctx.fillStyle = '#FF0000';
ctx.fillRect(0,0,80,100);
```

### 矩形

首先，我们回到矩形的绘制中。canvas提供了三种方法绘制矩形：

1. fillRect(x, y, width, height)
绘制一个填充的矩形
2. strokeRect(x, y, width, height)
绘制一个矩形的边框
3. clearRect(x, y, width, height)
清除指定矩形区域，让清除部分完全透明
<!--more-->

### 绘制路径

1. beginPath()
新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
2. closePath()
闭合路径之后图形绘制命令又重新指向到上下文中。
3. stroke()
通过线条来绘制图形轮廓。
4. fill()
通过填充路径的内容区域生成实心的图形。
5. moveTo(x, y)
将笔触移动到指定的坐标x以及y上。
6. lineTo(x, y)
绘制一条从当前位置到指定x以及y位置的直线。
7. arc(x, y, radius, startAngle, endAngle, anticlockwise)
画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。
8. quadraticCurveTo(cp1x, cp1y, x, y)
绘制贝塞尔曲线，cp1x,cp1y为控制点，x,y为结束点。
9. bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)
绘制二次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。
10. rect(x, y, width, height)
绘制一个左上角坐标为（x,y），宽高为width以及height的矩形。

### 色彩 Colors:
1. fillStyle = color
设置图形的填充颜色。
2. strokeStyle = color
设置图形轮廓的颜色。

### 透明度 Transparency:

1. globalAlpha = transparencyValue
这个属性影响到 canvas 里所有图形的透明度，有效的值范围是 0.0 （完全透明）到 1.0（完全不透明），默认是 1.0。

### 线型 Line styles:

1. lineWidth = value
设置线条宽度。
2. lineCap = type
设置线条末端样式。
3. lineJoin = type
设定线条与线条间接合处的样式。
4. miterLimit = value
限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度。
5. getLineDash()
返回一个包含当前虚线样式，长度为非负偶数的数组。
6. setLineDash(segments)
设置当前虚线样式。
7. lineDashOffset = value
设置虚线样式的起始偏移量。

### 渐变 Gradients:

1. createLinearGradient(x1, y1, x2, y2)
`createLinearGradient` 方法接受 4 个参数，表示渐变的起点 (x1,y1) 与终点 (x2,y2)。
2. createRadialGradient(x1, y1, r1, x2, y2, r2)
`createRadialGradient` 方法接受 6 个参数，前三个定义一个以 (x1,y1) 为原点，半径为 r1 的圆，后三个参数则定义另一个以 (x2,y2) 为原点，半径为 r2 的圆。
3. gradient.addColorStop(position, color)
`addColorStop` 方法接受 2 个参数，`position` 参数必须是一个 0.0 与 1.0 之间的数值，表示渐变中颜色所在的相对位置。例如，0.5 表示颜色会出现在正中间。`color` 参数必须是一个有效的 CSS 颜色值（如 #FFF， rgba(0,0,0,1)，等等）。

### 图案样式 Patterns:

1. createPattern(image, type)
该方法接受两个参数。Image 可以是一个 Image 对象的引用，或者另一个 canvas 对象。Type 必须是下面的字符串值之一：repeat，repeat-x，repeat-y 和 no-repeat。

### 阴影 Shadows:

1. shadowOffsetX = float
`shadowOffsetX` 和 `shadowOffsetY` 用来设定阴影在 X 和 Y 轴的延伸距离，它们是不受变换矩阵所影响的。负值表示阴影会往上或左延伸，正值则表示会往下或右延伸，它们默认都为 0。
2. shadowOffsetY = float
3. shadowBlur = float
`shadowBlur` 用于设定阴影的模糊程度，其数值并不跟像素数量挂钩，也不受变换矩阵的影响，默认为 0。
4. shadowColor = color
`shadowColor` 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色。

### Canvas 填充规则:

#### fill的传参

    "nonzero": non-zero winding rule, 默认值.
    "evenodd":  even-odd winding rule.

### 绘制文本：
1. fillText(text, x, y [, maxWidth])
在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的.
2. strokeText(text, x, y [, maxWidth])
在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的.

有样式的文本：
1. font = value
当前我们用来绘制文本的样式. 这个字符串使用和 CSS font 属性相同的语法. 默认的字体是 10px sans-serif。
2. textAlign = value
文本对齐选项. 可选的值包括：start, end, left, right or center. 默认值是 start。
3. textBaseline = value
基线对齐选项. 可选的值包括：top, hanging, middle, alphabetic, ideographic, bottom。默认值是 alphabetic。
4. direction = value
文本方向。可能的值包括：ltr, rtl, inherit。默认值是 inherit。

先进的文本测量：
1. measureText()
将返回一个 TextMetrics对象的宽度、所在像素，这些体现文本特性的属性。

### 绘制图片:

#### 获得需要绘制的图片：
1. HTMLImageElement
这些图片是由Image()函数构造出来的，或者任何的`<img>`元素
2. HTMLVideoElement
用一个HTML的 `<video>`元素作为你的图片源，可以从视频中抓取当前帧作为一个图像
3. HTMLCanvasElement
可以使用另一个 `<canvas>` 元素作为你的图片源。
4. ImageBitmap
这是一个高性能的位图，可以低延迟地绘制，它可以从上述的所有源以及其它几种源中生成。
这些源统一由 CanvasImageSource类型来引用。

#### 绘制
1. drawImage(image, x, y)
其中 image 是 image 或者 canvas 对象，x 和 y 是其在目标 canvas 里的起始坐标。
缩放 Scaling:
2. drawImage(image, x, y, width, height)
这个方法多了2个参数：width 和 height，这两个参数用来控制 当像canvas画入时应该缩放的大小
切片 Slicing:
3. drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)
第一个参数和其它的是相同的，都是一个图像或者另一个 canvas 的引用。其它8个参数最好是参照右边的图解，前4个是定义图像源的切片位置和大小，后4个则是定义切片的目标显示位置和大小。

### 状态的保存和恢复 Saving and restoring state:
- save()restore()
save 和 restore 方法是用来保存和恢复 canvas 状态的，都没有参数。Canvas 的状态就是当前画面应用的所有样式和变形的一个快照。

### 移动 Translating:
- translate(x, y)
translate 方法接受两个参数。x 是左右偏移量，y 是上下偏移量，如右图所示。

### 旋转 Rotating:
- rotate(angle)
这个方法只接受一个参数：旋转的角度(angle)，它是顺时针方向的，以弧度为单位的值。

### 缩放 Scaling:
- scale(x, y)
scale 方法接受两个参数。x,y 分别是横轴和纵轴的缩放因子，它们都必须是正值。值比 1.0 小表示缩小，比 1.0 大则表示放大，值为 1.0 时什么效果都没有。

### 变形 Transforms:
- transform(m11, m12, m21, m22, dx, dy)
setTransform(m11, m12, m21, m22, dx, dy)

### 叠加 
globalCompositeOperation = type

### 裁切路径 Clipping paths
clip()

### 操控动画 Controlling an animation
    window.setInterval(), window.setTimeout(),和window.requestAnimationFrame()
- setInterval(function, delay)
当设定好间隔时间后，function会定期执行。
- setTimeout(function, delay)
在设定好的时间之后执行函数
 
- requestAnimationFrame(callback)
告诉浏览器你希望执行一个动画，并在重绘之前，请求浏览器执行一个特定的函数来更新动画。

### ImageData 对象
ImageData对象中存储着canvas对象真实的像素数据，它包含以下几个只读属性：

    width

    图片宽度，单位是像素
    height
    图片高度，单位是像素
    data
    Uint8ClampedArray类型的一维数组，包含着RGBA格式的整型数据，范围在0至255之间（包括255）。

