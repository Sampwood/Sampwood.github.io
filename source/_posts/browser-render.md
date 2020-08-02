---
title: 浏览器渲染
categories:
  - coding
  - browser
tags:
  - 浏览器
date: 2020-01-22 10:53:31
update: 2020-01-22 10:53:31
---

### 我们为什么一再强调将css放在头部，将js文件放在尾部

在面试的过程中，经常会有人在回答页面的优化中提到将js放到body标签底部，原因是因为浏览器生成Dom树的时候是一行一行读HTML代码的，script标签放在最后面就不会影响前面的页面的渲染。
那么问题来了，既然Dom树完全生成好后页面才能渲染出来，浏览器又必须读完全部HTML才能生成完整的Dom树，script标签不放在body底部是不是也一样，因为dom树的生成需要整个文档解析完毕。

<!-- more -->

![\images\render-screenshot](/images/render-screenshot.png)

我们再来看一下chrome在页面渲染过程中的，绿色标志线是First Paint的时间。
纳尼，为什么会出现firstpaint，页面的paint不是在渲染树生成之后吗？其实现代浏览器为了更好的用户体验,渲染引擎将尝试尽快在屏幕上显示的内容。
它不会等到所有HTML解析之前开始构建和布局渲染树。部分的内容将被解析并显示。也就是说浏览器能够渲染不完整的dom树和cssom，尽快的减少白屏的时间。
假如我们将js放在header，js将阻塞解析dom，dom的内容会影响到First Paint，导致First Paint延后。
所以说我们会将js放在后面，以减少First Paint的时间，但是不会减少DOMContentLoaded被触发的时间。

### 从浏览器内核到事件循环

我们都知道js是单线程的，但js是支持异步的，所以浏览器内核必然是多线程or多进程的。那么你知道浏览器内核都有哪些线程来支撑页面工作的吗？

我们先看看浏览器的多进程架构吧。
以 Chrome 为例,它由多个进程组成,每个进程都有自己核心的职责,它们相互配合完成浏览器的整体功能，每个进程中又包含多个线程,一个进程内的多个线程也会协同工作,配合完成所在进程的职责。
Chrome 采用多进程架构,其顶层存在一个 Browser process 用以协调浏览器的其它进程。（详情看参考1）

浏览器主要有4个进程：主进程、第三方插件进程、GPU进程、渲染进程。
而渲染进程又称为浏览器渲染进程或浏览器内核,内部是多线程的。主要负责页面渲染,脚本执行,事件处理等。

浏览器内核的线程主要包括：GUI渲染线程、JS引擎线程、事件触发线程、定时触发器线程、异步http请求线程。

看到这是不是有中熟悉的感觉，js除了主线程之外还有一个宏任务和一个微任务队列。
其中宏任务（macrotask）主要包含：script(整体代码)、setTimeout、setInterval、I/O、UI交互事件、postMessage、MessageChannel、setImmediate(Node.js 环境)，
微任务（microtask）主要包含：Promise.then、MutaionObserver、process.nextTick(Node.js 环境)。

这里面setTimeout、 setInterval 是不是就是对应的 `定时触发器线程` 呢？
UI交互事件 对应 `事件触发线程`？ xhr 对应 `异步http请求线程` ？

这么对照着来看，就能很好的区分开宏任务和微任务。

js时间循环的运行机制是：
  - 执行一个宏任务（栈中没有就从事件队列中获取）
  - 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
  - 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
  - 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
  - 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

其中promise 构造函数的内容是立即执行的，then 中的函数会被放到对应的微任务列表中。

## 参考
1. [从 8 道面试题看浏览器渲染过程与性能优化](https://juejin.im/post/5e143104e51d45414a4715f7)
2. [DOMContentLoaded与load的区别](https://www.cnblogs.com/caizhenbo/p/6679478.html)
3. [DOMContentLoaded](https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded)
4. [第 10 题：常见异步笔试题，请写出代码的运行结果](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/7)