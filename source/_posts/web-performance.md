---
title: 网页性能优化
categories:
  - coding
  - vue
tags:
  - vue
  - 性能
date: 2019-10-08 18:47:33
update: 2019-10-08 18:47:33
---


记录下目前了解的一些优化网页性能的方法，包括初次加载时间等。

### webpack

目前开发的项目都是 `vue` 项目，`vue cli3` 默认使用 `webpack` 打包，所以从 `webpack` 方面入手优化项目是个挺好的角度。

#### preload prefetch

启动一个 `vue cli3` 创建的项目，发现在 `network` 中多了很多的请求，特殊的请求。这些请求被归类到 `other` 中。
而且这些资源被请求了两次。

查找了下资料发现，原来是因为这些 `other` 资源使用了 `preload` 或 `prefetch`。

那这是什么意思呢？

<!-- more -->

##### preload

`preload` 指令让浏览器以**高优秀级**预加载指定的资源。浏览器在遇到`preload` 后会已异步的方式下载资源，也不会阻塞页面渲染。

在下载完成之后存在内存中，在页面解析到script加载的时候，直接读取执行。

但是假如你在当前页面没有用到`preload` 的资源，Chrome 会报一个警告。

这其实是`preload` 和 `prefetch` 的区别。
`preload` 是以`高优先级`去加载当前页面将要使用的资源，而`prefetch` 是以`低优先级`去加载下个页面将会使用到的资源。

```xml
<link rel="preload" href="/main.js" as="script">

<script type="text/javascript" src="/main.js"></script>
```

##### prefetch

`Prefetch` 是一个低优先级的资源提示，允许浏览器在**后台（空闲时）**获取将来可能用得到的资源，并且将他们存储在浏览器的缓存中。
一旦一个页面加载完毕就会开始下载其他的资源，然后当用户点击了一个带有 prefetched 的连接，它将可以立刻从缓存中加载内容。
有三种不同的 `prefetch` 的类型，`link`，`DNS` 和 `prerender`，下面来详细分析。

`link` 用于加载资源，`DNS` 用于解析DNS，`prerender` 用于渲染页面。


##### 预先解析DNS

`DNS prefetching` 允许浏览器在用户浏览页面时在后台运行 DNS 的解析。如此一来，`DNS` 的解析在用户点击一个链接时已经完成，所以可以减少延迟。
可以在一个 `link` 标签的属性中添加 `rel="dns-prefetch'` 来对指定的 `URL` 进行 `DNS prefetching`

```xml
<!--在head标签中，越早越好-->
<link rel="dns-prefetch" href="https://cdn.bootcss.com/">
```

##### preconnect

`preconnect` 允许浏览器在一个 `HTTP` 请求正式发给服务器前预先执行一些操作，这包括 `DNS` 解析，`TLS` 协商，`TCP` 握手，这消除了往返延迟并为用户节省了时间。

```xml
<link href="https://cdn.domain.com" rel="preconnect" crossorigin>
```

#### defer和async

既然说到资源的加载，就不得不说到js文件的异步加载。

`defer` 和 `async` 是script标签的两个属性，用于在不阻塞页面文档解析的前提下，控制脚本的下载和执行。

`defer` 的执行时间是在所有元素解析完成之后，`DOMContentLoaded` 事件触发之前。

`async` 的执行时间是在当前JS脚本下载完成后，所以多个`async` script是执行顺序是不固定的。`async` 只能用于加载一些独立无依赖的代码，比如Google Analysis之类。

![script-load](/images/script-load.png)


所以，一个比较好的结构如下：
```xml
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Faster</title>
  <link rel="dns-prefetch" href="//cdn.com/">
  <link href="//cdn.com/" rel="preconnect" crossorigin>
  <link rel="preload" href="//js.cdn.com/currentPage-part1.js" as="script">
  <link rel="preload" href="//js.cdn.com/currentPage-part2.js" as="script">
  <link rel="preload" href="//js.cdn.com/currentPage-part3.js" as="script">

  <link rel="prefetch" href="//js.cdn.com/prefetch.js">
</head>
<body>

<script type="text/javascript" src="//js.cdn.com/currentPage-part1.js" defer></script>
<script type="text/javascript" src="//js.cdn.com/currentPage-part2.js" defer></script>
<script type="text/javascript" src="//js.cdn.com/currentPage-part3.js" defer></script>
</body>
</html>
```

#### vue.config.js

通过自定义chunks 把第三方包单独打包，来减小入口包的大小。

在`vue.config.js` 中设置：
```javascript
module.exports = {
  ...,
  chainWebpack: config => {
    // 因为是多页面，所以取消 chunks，每个页面只对应一个单独的 JS / CSS
    config.optimization.splitChunks({
      chunks: 'all',
      cacheGroups: {
        elementUI: {
          priority: 0,
          name: 'element-ui',
          test: /element-ui/,
          reuseExistingChunk: true
        },
        xlsx: {
          priority: 0,
          name: 'xlsx',
          test: /xlsx/,
          reuseExistingChunk: true
        },
        moment: {
          priority: 0,
          name: 'moment',
          test: /moment/,
          reuseExistingChunk: true
        }
      }
    })

    config.optimization.runtimeChunk = {
      name: 'manifest'
    }

    // 删除懒加载模块的 prefetch preload，降低带宽压力
    // config.plugins.delete('prefetch').delete('preload')
  }
}
```

就像上面写的，还可以把`prefetch` 和 `preload` 删除, 来减少服务器和带宽的压力

### CDN

对于某些不常变动，有比价庞大的第三方库来说（例如：echarts），可以考虑使用CDN资源，来减少服务器压力。
如果本身服务器访问不是很友好，或者没有做过加速处理，还是可以很大程度上提高网页访问速度的。

而且 `webpack` 也支持通过配置引入外部模块导入。

具体做法是：

首先在 `index.html` 中引入 `echarts` 的外部CDN：
```javascript
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <!-- others -->
    <link rel="preload" href="https://cdn.bootcss.com/echarts/4.2.1/echarts.min.js" as="script">
  </head>
  <body>
    <!-- others -->

    <!-- 加载echart -->
    <script type="text/javascript" src="https://cdn.bootcss.com/echarts/4.2.1/echarts.min.js"></script>
  </body>
</html>
```

然后在 `vue.config.js` 中，做如下改动：
```javascript
module.exports = {
  configureWebpack: {
    externals: {
      echarts: 'echarts' // echarts 从外部CDN中加载
    }
  }
}
```

### 第三方组件按需加载

针对某些大的依赖库，如果能按需加载，也可提高网页加载速度。

#### element-ui按需加载

`element-ui` 官网有按需加载的[文档](https://element.eleme.cn/#/zh-CN/component/quickstart).
按照说明配置下项目。

首先，安装`babel-plugin-component`
```bash
npm install babel-plugin-component -D
```

然后修改 `babel.config.js` 文件：
```javascript
module.exports = {
  presets: ['@vue/app'],
  plugins: [
    [
      'component',
      {
        libraryName: 'element-ui',
        styleLibraryName: 'theme-chalk'
      }
    ]
  ]
}
```

最后就是在 `src/main.js` 中按需引入组件。


### 添加骨架屏

添加骨架屏能让页面更快的出现内容，提高用户体验。

例如这样的：
```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <!-- 骨架屏的样式 -->
    <style type="text/css">
      body {
        margin: 0;
      }
      .mp-skeleton {
        height: 100vh;
        background-color: #f4f5f5;
      }
      .mp-skeleton-header {
        height: 97px;
        background: linear-gradient(90deg,#132462,#0f1839);
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
      }
      .mp-skeleton-header__content {
        height: 100%;
        max-width: 1200px;
        margin: auto;
        display: flex;
        align-items: center;
      }
      .mp-skeleton-header__content .mp-skeleton-bar {
        background: linear-gradient(90deg,#0d1a48,#152a75,#0d1a48);
        height: 28px;
        border-radius: 3px;
      }
      .mp-skeleton-container {
        box-sizing: border-box;
        height: 120px;
        max-width: 1200px;
        margin: auto;
        background-color: #fff;
        margin-top: 52px;
        border-radius: 3px;
        padding: 4px 20px;
        overflow: hidden;
      }
      .mp-skeleton-bar {
        height: 16px;
        margin: 16px 0;
        background: linear-gradient(90deg,#fff,#edeff1,#fff);
        background-size: 480px 480px;
        animation: skeleton-stripes-data .6s linear infinite;
      }
      .mp-skeleton-bar:first-of-type {
        width: 40%;
      }
      .mp-skeleton-bar:nth-of-type(2), .mp-skeleton-bar:nth-of-type(3) {
        width: 80%;
      }
      @-moz-keyframes skeleton-stripes-data {
        0% {
          background-position: 0 0;
        }
        to {
          background-position: 480px 0;
        }
      }
      @-webkit-keyframes skeleton-stripes-data {
        0% {
          background-position: 0 0;
        }

        to {
          background-position: 480px 0;
        }
      }
      @-o-keyframes skeleton-stripes-data {
        0% {
          background-position: 0 0;
        }

        to {
          background-position: 480px 0;
        }
      }
      @keyframes skeleton-stripes-data {
        0% {
          background-position: 0 0;
        }

        to {
          background-position: 480px 0;
        }
      }
    </style>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but matpool doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <!-- 骨架元素 -->
    <div id="skeleton" class="mp-skeleton">
      <div class="mp-skeleton-header">
        <div class="mp-skeleton-header__content">
          <div class="mp-skeleton-bar"></div>
        </div>
      </div>
      <div class="mp-skeleton-container">
        <div class="mp-skeleton-bar"></div>
        <div class="mp-skeleton-bar"></div>
        <div class="mp-skeleton-bar"></div>
      </div>
    </div>

    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```


## 参考
1. [带你玩转prefetch, preload, dns-prefetch，defer和async](https://segmentfault.com/a/1190000011577248)
2. [[译] 资源提示 —— 什么是 Preload，Prefetch 和 Preconnect？](https://github.com/fi3ework/blog/issues/32)
3. [记一次vue+element+echarts项目的优化（如何轻松将项目性能提升70%）](https://juejin.im/post/5b0033c9518825056508075f)
4. [Vue项目使用CDN优化加载](https://blog.csdn.net/jiaojiao51290/article/details/81381668)
5. [vue-cli3.0 资源加载的优化方案](https://segmentfault.com/a/1190000016178566#articleHeader3)