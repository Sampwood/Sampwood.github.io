---
title: prerender-vue
categories:
  - coding
  - vue
tags:
  - vue
  - seo
  - environment
date: 2020-03-18 16:18:03
update: 2020-03-18 16:18:03
---


### 场景

目前大部分接触到的页面都会使用SPA来实现。但是SPA会有个问题，就是对于SEO的支持不是很好。

对于vue项目来说，可以使用nuxt一把梭😹。
但对于简单的场景来说，其实也可以用预渲染的方式来只对首页和文档部分做静态渲染。
这样既满足了SEO的支持，也和原本vue的写法一样。

<!-- more -->

### 依赖

#### prerender-spa-plugin

预渲染依赖 `prerender-spa-plugin` 插件。

`prerender-spa-plugin` 是 `webpack` 的插件，所以用 `webpack` 打包的单页面（`react`，`vue`，`angular`等）都能使用。

`prerender-spa-plugin` 原理就是使用浏览器内核预先加载页面渲染数据得到完整页面后生成完整的`html`文件。

#### sitemap.js

百度爬虫非常喜欢Sitemap，Sitemap协议使你能够告知搜索引擎网站中可供抓取的网址，sitemap的生成就是让搜索引擎更好的去访问网站从而给网站的收录产生一个好的作用。

所以用`sitemap` 库来自动生成 `sitemap.xml` 文件对SEO有很大帮助。

### 实践

#### 安装依赖

```
yarn add prerender-spa-plugin sitemap -D 
```

#### 将vue router mode属性改为history

mode必须为history，如果不改为history，在hash模式下，prerender-spa-plugin打包出来的静态html只有首页的html是完整的，其他页面还是用的首页骨架，然后动态生成生成html替换，就达不到seo的效果。

#### 配置webpack文件

修改build/webpack.prod.conf.js 文件：
```
const path = require('path')
const resolve = dir => path.join(__dirname, '..', dir);
// pre-render
const PrerenderSPAPlugin = require('prerender-spa-plugin')
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer
const VueLoaderPlugin = require('vue-loader/lib/plugin')
const routes = ['/'] // 确定需要预渲染的页面路由和生成sitemap的路由，这两个也可以分开
// sitemap url容器
const sitemap = require('sitemap');
const siteMapUrls = [];

const webpackConfig = merge(baseWebpackConfig, {
    plugins: [
      // pre render
      new PrerenderSPAPlugin({
        staticDir: path.join(__dirname, '../dist'),
        // Output directory
        outputDir: path.join(__dirname, '../dist/rendered'),
        routes,
        postProcess (context) {
          //content 参数
          const {originalRoute, route, html} = context;
          //将路由添加到全局sitemap容器
          siteMapUrls.push(originalRoute);

          //当当前路由为最后一个生成路由时  
          if (route === routes[routes.length - 1]) {
            //去除重复的链接
            let currentSiteMapUrls = Array.from(new Set(siteMapUrls));
            // //过滤掉链接中的锚点后内容
            currentSiteMapUrls = currentSiteMapUrls.map(url => {
              const isMao = url.indexOf('#') > -1;
              //生成sitemap所需数据，具体参数参详sitemap.js官网
              return {url: isMao ? url.split('#')[0] : url, changefreq: 'weekly', priority: 1, lastmod: new Date().toLocaleDateString()}
            });
            //生成siteMap文件
            const sm = sitemap.createSitemap({
              //路由前缀地址，全地址自动不会添加hostname
              hostname: 'https://matpool.com',
              cacheTime: 600000,  //600 sec (10 min) cache purge period
              urls: currentSiteMapUrls
            });
            //将sitemap文件添加搭配打包文件夹dist中
            fs.writeFileSync(resolve('dist/sitemap.xml'), sm.toString());
          }
          // 解决闪屏问题
          context.html = html.replace('id="app"', 'id="app" data-server-rendered="true"');
          //返回当前contet对象
          return context
        },
        renderer: new Renderer({
          inject: {
            foo: 'bar'
          },
          headless: true,
          renderAfterDocumentEvent: 'render-event',
          //puppeteer参数，标签意思：完全信任在Chrome中打开的内容
          args: ['--no-sandbox', '--disable-setuid-sandbox']
        })
      }),
    ]
})
```

上面webpack文件中 `postProcess` 是用来配合`sitemap`库生成`sitemap.xml`文件的。
PrerenderSPAPlugin 插件中其他代码是用来生成预渲染页面的。

#### 修改main.js

```javascript
new Vue({
  el: '#app',
  router,
  store,
  i18n,
  mounted() {
    // 这个是关键，触发事件来引导预渲染。"render-event" 得和上面的PrerenderSPAPlugin.renderer.renderAfterDocumentEvent一致
    document.dispatchEvent(new Event('render-event'))
  },
  render: h => h(App)
})
```

### 遇到的问题

#### 依赖包不能下载

`prerender-spa-plugin` 依赖 google 的 `puppeteer` 库。所以install的时候需要翻墙，或者设置淘宝源。

#### Linux不能正常打包

本地打包没有问题，linux服务器打包可能会出现ERROR: Fail to lauunch chrome!

原因一：linux 环境缺少一些[依赖](https://github.com/puppeteer/puppeteer/blob/master/docs/troubleshooting.md#chrome-headless-doesnt-launch-on-unix)

所以需要先在Linux上装好这些依赖。

因为我是用docker来构建部署的，这里提供下dockerfile：

```Dockerfile
FROM ubuntu:18.04
LABEL maintainer="sampwood@163.com"
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list; \
  apt-get update; \
  DEBIAN_FRONTEND=noninteractive \
  apt-get install -y --no-install-recommends \
  ca-certificates \
  bash \
  curl \
  git \
  apt-utils
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash
RUN apt-get install -y nodejs
RUN curl -sL https://sentry.io/get-cli/ | bash
RUN npm config set registry https://registry.npm.taobao.org
RUN npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
RUN npm install -g yarn@1.16.0 standard-version@7.1.0
RUN yarn config set registry https://registry.npm.taobao.org
RUN yarn config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/

RUN  apt-get update \
  && apt-get install -y gconf-service libasound2 libatk1.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget \
  && rm -rf /var/lib/apt/lists/*
```
#### 闪屏

在预渲染构建完成之后，访问页面，可能会出现闪屏的现象。

解决方法：在 `PrerenderSPAPlugin.postProcess` 中加入下面这段代码（上面的例子中已经加了）
```javascript
context.html = html.replace('id="app"', 'id="app" data-server-rendered="true"');
```

#### 其他页面刷新时不能正常访问

在预渲染页面之外的页面刷新时，可能会出现不能正常渲染的情况。

解决方法：配置 `nginx` 文件:
```nginx
http {
  server {
    # pre-rendered routes
    location = / {
        try_files $uri /rendered/index.html;
    }

    # all other dynamic routes
    location / {
      index index.html;
      try_files $uri $uri/ /index.html;
    }
  }
}
```

这里面的 `/rendered/` 文件夹要和上面`webpack.prod.conf.js` 中 `PrerenderSPAPlugin.outputDir` 保持一致


## 参考

1. [国内下载安装 Puppeteer 的方法](https://brickyang.github.io/2019/01/14/%E5%9B%BD%E5%86%85%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85-Puppeteer-%E7%9A%84%E6%96%B9%E6%B3%95/)
2. [vue项目seo问题简单解决，并生成sitemap](https://juejin.im/post/5dcbc2196fb9a0606f094186#heading-2)
3. [puppeteer Troubleshooting](https://github.com/puppeteer/puppeteer/blob/master/docs/troubleshooting.md)
