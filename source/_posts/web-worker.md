---
title: web worker
categories:
  - coding
  - js
tags:
  - 浏览器
date: 2020-08-02 22:54:33
update: 2020-08-02 22:54:33
---

今天看到一篇文章讲解了web worker的技术方案。很有收获，虽然目前虽然不一定能用上。

<!-- more -->

### 简介

> Web Worker 作为浏览器多线程技术, 在页面内容不断丰富, 功能日趋复杂的当下, 成为缓解页面卡顿, 提升应用性能的可选方案.

web worker可以创建一个新的线程来执行重度逻辑，xhr，IO等操作。因为它不会影响主线程的运行，所以其在某些场景下可以解决页面卡顿，提升渲染性能。

官方文档是 MDN 的 Web Workers API. 其对 Web Worker 的表述是:

> Web Workers makes it possible to run a script operation in a background thread separate from the main execution thread of a web application.

### 兼容性

web worker的浏览器兼容性还不错，主流浏览器在几年前就支持 Worker。

> 可见当下浏览器已经较好地支持 Worker, 只要对 0.09% 的不支持浏览器做好回退策略(如展示一个 tip), Worker 可以放心地应用到前端业务中.

### 语法

```javascript
// main.js
const worker = new Worker('./worker.js');
worker.postMessage('hello');
worker.onmessage = event => {
    console.log(event.data); // 'world'
}

// worker.js
self.onmessage = event => {
    console.log(event.data); // 'hello'
    postMessage('world');
}
```

主要流程为:
- 主线程调用 new Worker(url) 创建 Worker 实例, url 为 Worker JS 资源 url.
- 主线程调用 postMessage 发送 hello, 
- 主线程创建监听：在 onmesssage 中监听 Worker 线程消息.
- Worker 线程在 onmessage 中监听主线程消息, 收到主线程的 hello; 
- Worker 通过 postMessage 回复 world.
- 主线程在消息回调中收到 Worker 的 world 信息.

语法也比较简单。但是在实用时，还是对数据通信这部分做一下封装。也有库做了这些事，可以搜搜。

#### 运行环境差异

worker 和 主线程的运行环境还是有一些差异的。最主要就是worker 是不支持 UI 的，所以dom相关的api是不存在的。而且worker 的global对象不是主线程的window对象。Worker 线程运行时, 会创建独立于主线程的 JS 运行环境, 称之为 DedicatedWorkerGlobalScope。

### 通信速度等

对web worker通信速度等测试就由专业人员来进行了~~


### service work

看到这，有没有想起另一个独立注册运行的功能 service work呢？

service work和web work还是有一定的区别的。service work会脱离页面运行，就是在页面没有访问到时也会独立运行。

> Service workers本质上充当Web应用程序与浏览器之间的代理服务器，也可以在网络可用时作为浏览器和网络间的代理。它们旨在（除其他之外）使得能够创建有效的离线体验，拦截网络请求并基于网络是否可用以及更新的资源是否驻留在服务器上来采取适当的动作。

service worker是对页面内容做缓存和更新，相当于创建一个可以offline工作的app。

而web worker主要功能是把复杂的占用cpu时间长的逻辑抽离出来，减少页面卡顿，提高页面渲染性能。

## 参考
- [【第2015期】Web Worker 文献综述](https://mp.weixin.qq.com/s/MyRRIbn-UoruVD1dpvD-QQ)