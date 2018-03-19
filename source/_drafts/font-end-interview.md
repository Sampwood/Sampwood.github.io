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

1. 200：正常
2. 204：正常，但没有返回内容（就应该没有内容返回的状况） 
3. 206：正常，服务器已经完成了部分GET请求（客户端进行了范围请求） 
4. 301：永久重定向
5. 302：临时重定向
6. 303：请求资源在另一个URI，应使用GET定向获取请求资源
7. 304：客户端发送附带条件的请求，条件不满足（似乎和重定向没有关系）
8. 307：临时重定向，不会从POST变为GET
9. 400：请求报文存在语法错误或参数错误，服务器不理解
10. 401：发送的请求需要有HTTP认证信息或认证失败了
11. 403：服务器禁止访问
12. 404：请求的资源未找到
13. 500：服务器错误
14. 503：服务器超负载或正停机维护

**参考**：http://blog.csdn.net/q1056843325/article/details/53147180

#### 查找页面中嵌套最深的元素

#### 如何提高页面性能（css渲染）

#### 盒模型的理解

