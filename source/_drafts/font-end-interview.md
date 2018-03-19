---
title: font-end interview
tags:
---

### 代码

#### 模拟Promise的实现

```javascript
new MyPromise((res, rej) => {
  // some codes

  if (/* 异步操作 */) {
    res(value);
  } else {
    rej(error);
  }
}).then(() => {})
  .catch(() => {})

function MyPromise(fn) {
  // TODO
}

MyPromise.prototype.then = function (res) {
  // TODO
}
MyPromise.prototype.catch = function (rej) {
  // TODO
}

```

### 问题

#### 网页渲染过程

1. 通过URI，定位服务器IP；
2. 发送http请求；
3. 建立http链接；
4. 服务器返回客户端请求内容；
5. 浏览器对返回的数据进行解析；
6. html/xhtml解析成DOM，css解析成cssom，遇到script标签即执行，停止页面解析；
7. 结合DOM和cssom生成render tree，布局render树，绘制render树；
8. 回流和重绘

**参考**：http://blog.csdn.net/xiaozhuxmen/article/details/52014901

#### 前后端交互的细致过程

#### javascript的gc（垃圾回收机制）

#### 页面元素的垂直居中

#### 服务器状态码

#### 查找页面中嵌套最深的元素

#### 如何提高页面性能（css渲染）

#### 盒模型的理解

