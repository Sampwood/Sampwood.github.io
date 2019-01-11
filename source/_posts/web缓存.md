---
title: web缓存
categories:
  - coding
  - js
tags:
  - 缓存
date: 2018-06-18 15:48:36
update: 2018-06-18 15:48:36
---


在前端开发中，性能一直都是被大家所重视的一点，然而判断一个网站的性能最直观的就是看网页打开的速度。其中提高网页反应速度的一个方式就是使用缓存。一个优秀的缓存策略可以缩短网页请求资源的距离，减少延迟，并且由于缓存文件可以重复利用，还可以减少带宽，降低网络负荷。

在这里整理一些web缓存的方法。

### 缓存分类

![缓存分类](/images/classify.png)

<!-- more -->

### 浏览器缓存

对于浏览器端的缓存来讲，这些规则是在HTTP协议头和HTML页面的Meta标签中定义的。他们分别从**新鲜度**和**校验值**
两个维度来规定浏览器是否可以直接使用缓存中的副本，还是需要去源服务器获取更新的版本。

对应的来说就是header中的4个参数：`Cache-Control`, `Expires`, `Last-Modified`, `ETag`。

#### 本地缓存阶段

##### `Cache-Control`: 
- max-age: （单位为s）指定设置缓存最大的有效时间，定义的是时间长短。当浏览器向服务器发送请求后，在max-age这段时间里浏览器就不会再向服务器发送请求了。
- s-maxage: 单位为s）同max-age，只用于共享缓存（比如CDN缓存）。
- public: 指定响应会被缓存，并且在多用户间共享。表示缓存的版本可以被代理服务器或者其他中间服务器识别。
- private: 响应只作为私有的缓存（见下图），不能在用户间共享。如果要求HTTP认证，响应会自动设置为private。意味着这个文件对不同的用户是不同的。只有用户自己的浏览器能够进行缓存，公共的代理服务器不允许缓存。
- no-cache: 指定不缓存响应，表明资源不进行缓存
- no-store: 绝对禁止缓存
- must-revalidate: ?

##### `Expires`：指定缓存到期GMT的绝对时间; 缓存过期时间，用来指定资源到期的时间，是服务器端的具体的时间点。

如果设了`max-age`，`max-age`就会覆盖`expires`。如果`expires`到期需要重新请求。

#### 协商缓存阶段

##### `Last-Modified`和`if-modified-since`
这是一对报文头，属于http 1.0。

last-modified是WEB服务器认为对象的最后修改时间，比如文件的最后修改时间，动态页面的最后产生时间。

##### `Etag`和`If-None-Match`
这是一对报文，属于http 1.1。

根据实体内容生成一段hash字符串，标识资源的状态，由服务端产生。浏览器会将这串字符串传回服务器，验证资源是否已经修改。

Etag 主要为了解决 Last-Modified 无法解决的一些问题：

1、一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET；

2、某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)；

3、某些服务器不能精确的得到文件的最后修改时间，这样就无法通过最后修改时间判断资源是否更新 

### 服务端缓存

#### CDN缓存
CDN缓存，也叫网关缓存、反向代理缓存。浏览器先向CDN网关发起WEB请求，网关服务器后面对应着一台或多台负载均衡源服务器，会根据它们的负载请求，动态地请求转发到合适的源服务器上。

CDN边缘节点缓存策略因服务商不同而不同，但一般都会遵循http标准协议，通过http响应头中的Cache-control: max-age的字段来设置CDN边缘节点数据缓存时间。

当客户端向CDN节点请求数据时，CDN节点会判断缓存数据是否过期，若缓存数据并没有过期，则直接将缓存数据返回给客户端；否则，CDN节点就会向源站发出回源请求（back to the source request），从源站拉取最新数据，更新本地缓存，并将最新数据返回给客户端。

### HTML5缓存

#### application cache离线缓存
离线缓存有如下优势：
- 用户可离线访问你的应用，这对于无法随时保持联网状态的移动终端用户来说尤其重要
- 用户访问本地的缓存文件，通常意味着更快的访问速度
- 仅仅加载被修改过的资源，避免同一资源对服务器多次的请求，大大降低了对服务器的访问压力

实现方法是在`html`标签中配置指定的`manifest`文件(`<html manifest="demo.manifest">`)：
```
CACHE MANIFEST
# wanz app v1

# 指明缓存入口
CACHE:
index.html
style.css
images/logo.png
scripts/main.js

# 以下资源必须在线访问
NETWORK:
login.php

# 如果index.php无法访问则用404.html代替
FALLBACK:
/index.php /404.html
```

在文件中配合js使用：
```javascript
window.applicationCache.addEventListener('updateready', function(e) {
  if (window.applicationCache.status === window.applicationCache.UPDATEREADY) {
    window.applicationCache.swapCache();
  }
});
```

这个过程中有几个问题需要注意：

- 如果服务器对离线的资源进行了更新，那么必须更新manifest文件之后这些资源才能被浏览器重新下载，如果只是更新了资源而没有更新manifest文件的话，浏览器并不会重新下载资源，也就是说还是使用原来离线存储的资源。
- 对于manifest文件进行缓存的时候需要十分小心，因为可能出现一种情况就是你对manifest文件进行了更新，但是http的缓存规则告诉浏览器本地缓存的manifest文件还没过期，这个情况下浏览器还是使用原来的manifest文件，所以对于manifest文件最好不要设置缓存。
- 浏览器在下载manifest文件中的资源的时候，它会一次性下载所有资源，如果某个资源由于某种原因下载失败，那么这次的所有更新就算是失败的，浏览器还是会使用原来的资源。
- 在更新了资源之后，新的资源需要到下次再打开app才会生效，如果需要资源马上就能生效，那么可以使用`window.applicationCache.swapCache()`方法来使之生效，出现这种现象的原因是浏览器会先使用离线资源加载页面，然后再去检查manifest是否有更新，所以需要到下次打开页面才能生效。
- 配置manifest的**index.html文件也会被缓存**。

#### cookie

`cookie`非常小，它的大小限制为4KB左右。主要用途有保存登录信息，做身份认证。
cookie是不可跨域的，他会根据域名来区分是向哪个服务器发送数据。

#### sessionStorage
sessionStorage和localStorage是html5提供的两种本地存储方式，只会存在本地，大小要比cookie大

sessionStorage是用户从打开回话窗到关闭会话窗这一段时间有效，关闭之后存的数据就会被删除。

**刷新页面数据依旧存在，但不同会话窗口之间不同享**

#### localStorage

严格说来localStorage更像是cookie一类的本地数据存储。但在标准缓存之外，开发人员可以用浏览器的一些功能来实现自定义的客户端“缓存”。

与sessionStroage主要的区别是存储时间和作用域。

LocalStorage是一种本地存储的公共资源，域名下很多应用共享这份资源会有风险；
LocalStorage是以页面域名划分的，如果有多个等价域名之间的LocalStorage不互通，则会造成缓存多份浪费。

LocalStorage在PC上的兼容性不太好，而且当网络速度快、协商缓存响应快时使用localStorage的速度比不上304。
并且不能缓存css文件。而移动端由于网速慢，使用localStorage要快于304。


## 参考
1. [浅谈Web缓存](http://www.alloyteam.com/2016/03/discussion-on-web-caching/)
2. [缓存策略](http://imweb.io/topic/55c6f9bac222e3af6ce235b9)
3. [前端工程精粹（一）：静态资源版本更新与缓存](http://www.infoq.com/cn/articles/front-end-engineering-and-performance-optimization-part1)
4. [详说 Cookie, LocalStorage 与 SessionStorage](http://jerryzou.com/posts/cookie-and-web-storage/)
5. [前端存储浅谈——cookie、sessionStorage、localStorage](https://segmentfault.com/a/1190000012478396)
6. [cookie、sessionStorage、localStorage 详解及应用场景](https://segmentfault.com/a/1190000010400892)
