---
title: 浏览器渲染
categories:
  - coding
  - html
tags:
  - 浏览器
date: 2020-01-22 10:53:31
update: 2020-01-22 10:53:31
---

### 我们为什么一再强调将css放在头部，将js文件放在尾部

在面试的过程中，经常会有人在回答页面的优化中提到将js放到body标签底部，原因是因为浏览器生成Dom树的时候是一行一行读HTML代码的，script标签放在最后面就不会影响前面的页面的渲染。
那么问题来了，既然Dom树完全生成好后页面才能渲染出来，浏览器又必须读完全部HTML才能生成完整的Dom树，script标签不放在body底部是不是也一样，因为dom树的生成需要整个文档解析完毕。

![\images\render-screenshot](/images/render-screenshot.png)

我们再来看一下chrome在页面渲染过程中的，绿色标志线是First Paint的时间。
纳尼，为什么会出现firstpaint，页面的paint不是在渲染树生成之后吗？其实现代浏览器为了更好的用户体验,渲染引擎将尝试尽快在屏幕上显示的内容。
它不会等到所有HTML解析之前开始构建和布局渲染树。部分的内容将被解析并显示。也就是说浏览器能够渲染不完整的dom树和cssom，尽快的减少白屏的时间。
假如我们将js放在header，js将阻塞解析dom，dom的内容会影响到First Paint，导致First Paint延后。
所以说我们会将js放在后面，以减少First Paint的时间，但是不会减少DOMContentLoaded被触发的时间。


## 参考
1. [从 8 道面试题看浏览器渲染过程与性能优化](https://juejin.im/post/5e143104e51d45414a4715f7)
2. [DOMContentLoaded与load的区别](https://www.cnblogs.com/caizhenbo/p/6679478.html)
3. [DOMContentLoaded](https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded)