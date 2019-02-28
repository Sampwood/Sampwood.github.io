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

1. 通过URI，定位服务器IP（DNS解析）；
2. 建立TCP/IP连接（TCP三次握手）；
3. 发送http请求；
4. 服务器返回客户端请求内容；
5. 浏览器对返回的数据进行解析；
6. html/xhtml解析成DOM，css解析成cssom，遇到script标签即执行，停止页面解析；
7. 结合DOM和cssom生成render tree，布局render树，绘制render树；
8. 回流和重绘

**参考**：

1. [浏览器加载、解析、渲染的过程](http://blog.csdn.net/xiaozhuxmen/article/details/52014901)

#### 前后端交互的细致过程

当我们在浏览器的地址栏输入 www.baidu.com ，然后回车，回车这一瞬间到看到页面到底发生了什么呢？

1. 域名解析，根据域名找到服务器的IP -->
2. 发起TCP的3次握手 -->
3. 建立TCP连接后发起http请求 --> 
4. 服务器响应http请求，浏览器得到html代码 -->
5. 浏览器解析html代码，并请求html代码中的资源（如js、css、图片等） --> 
6. 浏览器对页面进行渲染呈现给用户

每次都请求都会经过:

1. 客户端的应用层（http协议）-->  
2. 客户端的传输层（tcp或udp协议） -->
3. 客户端的网络层（ip协议） --> 
4. 客户端的链路层（网卡，路由器等） -->  
5. 经过dns解析，穿越多个isp（互联网服务提供商，移动，联通，电信等），各种数据交换，找到了服务器-->
6. 服务器的链路层  -->
7. 服务器的网络层  -->
8. 服务器的传输层  -->
9. 服务器的应用层。

服务器响应  与请求相反，倒过来看即可。

##### 缓存
1. 强制缓存：服务端`header`中的`Cache-Control` 和 `Expires`
2. 对比缓存：`header`中的`Last-Modified(服务端)／If-Modified-Since(客户端)`和`Etag(服务端)/If-None-Match(客户端)`

##### 压缩
服务端可以根据`Accept-Ecoding`头来返回响应的压缩资源，同时设置`Content-Encoding`头告诉浏览器你用了什么压缩方式。

##### 断点续传

断点续传的原理就是利用HTTP头中的Range来告诉服务器我所上传的文件的内容区间。

**参考**: 

1. [WEB程序的前后端数据交互流程](https://segmentfault.com/a/1190000011235491)
2. [通过手写文件服务器，说说前后端交互](通过手写文件服务器，说说前后端交互)
3. [一次完整的HTTP事务是怎样一个过程？](http://blog.51cto.com/linux5588/1351007)
4. [从输入URL到页面加载完成的过程中都发生了什么事情？](https://segmentfault.com/q/1010000000489803)

#### javascript的gc（垃圾回收机制）

#### 页面元素的垂直居中

html结构如下：
```html
<div class="parent">  
    <div class="child">
        <div class="content">Content goes here</div>
    </div>
</div>  
```

1. 使用`table`的`vertical-align: middle`属性：
```
.parent {
    display: table;
}

.child {
    display: table-cell;
    vertical-align: middle;
}
```

2. 使用`position: absolute`:
2.1.
```
.parent {
    position: relative;
}

.child {
    position: absolute;
    top: 50%;

    height: 20px; //自定义高度
    margin-top: -10px; //固定为元素高度的一半
}
```

2.2.
```
.parent {
  position: relative;
}

.child {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;

    height: 40px; //自定义高度
    width: 70%; //自定义宽度
}
```

2.3.
```
.parent {
  position: relative;
}

.child {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
```

3. 使用`position: relative`:
```
.child {
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}
```

4. 使用`flex`:
```
.parent {
    display:flex;/*Flex布局*/
    display: -webkit-flex; /* Safari */
    align-items:center;/*指定垂直居中*/
}
```

5. 使用`:before`:
```
.parent:before {
  content:'';
  display:inline-block;
  vertical-align:middle;
  height:100%;
}
.child {
  display:inline-block;  
  vertical-align:middle;  
  line-height:60px;      
  background:yellow;
}
```

6. 将单行文本置中：`line-height` = `height`

#### http状态码

状态码 | 响应类别 | 原因短语
---|---|---
1XX | 信息性状态码（Informational）| 服务器正在处理请求
2XX | 成功状态码（Success） | 请求已正常处理完毕
3XX | 重定向状态码（Redirection）| 需要进行额外操作以完成请求
4XX | 客户端错误状态码（Client Error）|  客户端原因导致服务器无法处理请求
5XX | 服务器错误状态码（Server Error）|  服务器原因导致处理请求出错

1. 200：成功，并返回数据
2. 201：已创建
3. 202：已接受
4. 203：成为，但未授权
5. 204：正常，但没有返回内容（就应该没有内容返回的状况）
6. 205: 成功，重置内容 
7. 206：正常，服务器已经完成了部分GET请求（客户端进行了范围请求） 
8. 301：永久重定向
9. 302：临时重定向
10. 303：请求资源在另一个URI，应使用GET定向获取请求资源
11. 304：资源未修改，可使用缓存
12. 307：临时重定向，不会从POST变为GET
13. 400：请求报文存在语法错误或参数错误，服务器不理解
14. 401：发送的请求需要有HTTP认证信息或认证失败了
15. 403：服务器禁止访问
16. 404：请求的资源未找到
17. 500：服务器错误
18. 503：服务器超负载或正停机维护

**参考**：http://blog.csdn.net/q1056843325/article/details/53147180

#### 查找页面中嵌套最深的元素

#### 如何提高页面性能（css渲染）

#### 盒模型的理解

css盒子模型，又称为框模型，包含了元素内容（`content`）、内边框（`padding`）、边框（`border`）、外边框（`margin`）几个要素。

盒子模型有两种，W3C和IE盒子模型：
1. W3C定义的盒子模型包括`margin`、`border`、`padding`、`content`，元素的`width=content`的宽度
2. IE盒子模型与W3C的盒子模型唯一区别就是元素的宽度，元素的`width=content+padding+border`

CSS3中新增了一个样式box-sizing，包含两个属性content-box 和 border-box。
1. `content-box`元素的`width=content`(W3C盒子)
2. `border-box`元素的`width=content+padding+border`(IE盒子)

**css 外边距合并（叠加）**:

1.对于行级元素，`margin-top`和`margin-bottom`对于上下元素无效，`margin-left`和`margin-right`有效；
2.两个上下方向相邻的元素框垂直相遇时，外边距(margin)会合并，合并后的外边距的高度等于两个发生合并的外边距中较高的那个边距值，margin-left和margin-right不会合并。


#### 跨域之CORS

#### cookie vs storage

#### es6

#### import vs require


**参考**:
1. [Web前端面试指导(九)：盒子模型你是怎么理解的？](http://blog.csdn.net/lxcao/article/details/52620453)
2. [css 盒子模型理解](https://www.cnblogs.com/clearsky/p/5696286.html)

## 参考
1. [前端笔试题面试题记录（上）](https://juejin.im/post/5aad40e4f265da237f1e12ed?utm_source=gold_browser_extension)
