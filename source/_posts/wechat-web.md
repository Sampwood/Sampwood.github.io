---
title: 微信网页开发笔记
categories:
  - coding
  - wechat
tags:
  - html5
  - vue
  - mobile
date: 2019-08-19 23:39:06
update: 2019-08-19 23:39:06
---


这几天开发了一个微信服务号，也算是入门了，纪录下其中的过程和遇到的坑。

### 开发环境

要在微信的平台上进行开发，需要一些基础的环境。其中包括，微信公众号，服务器，域名。

#### 域名

域名需要备案，且需要配置在 `公众平台官网中的“开发 - 接口权限 - 网页服务 - 网页帐号 - 网页授权获取用户基本信息”的配置选项中`

域名的配置需要验证一个文件，这个配置下 `nginx` 就可以了。

当然本地开发的时候是不可能时时发布的，所以可以通过修改本地 `host` 的方式来快速开发。

<!-- more -->

#### 开发工具

除此之外，微信提供了一个 `微信web开发者工具` （桌面应用），用于开发和调试基于微信的网页。

正常开发移动端只要在浏览器中选用 `响应式开发工具` 就可以切换到 移动端的机型。
所以，这个微信开发工具主要的价值是体现在**它能如同微信一样获取用户授权，从而拿到部分用户信息**。

要使用开发工具，还得把自己加到 `微信公众平台-开发-开发者工具-web开发者工具-开发者微信号` 中。

#### js-sdk

和上面差不多，正常的移动端开发是用不上 `微信js-sdk` 的。

它的作用是能通过 `js` 调用一些系统的功能，例如：自定义分享内容，图像接口，地理位置等。

这次开发的功能只用到了 `自定义分享内容` 的功能。（PS：就是这个分享略坑，坑了我一天时间。后面再细说）

#### 样式库

微信有提供一套web样式库- `weui`，如果没有设计或者设计风格贴近微信的可以用一下，或者参考下它的组件设计。

因为这次设计风格和这个样式库差很多，而且页面也比较少。所以就都自己画了。



### 微信网页授权

> 如果用户在微信客户端中访问第三方网页，公众号可以通过微信网页授权机制，来获取用户基本信息，进而实现业务逻辑。

这边只说下前端的逻辑。

网页授权的逻辑：
1. 用户同意授权，获取code； 也就是打开特定的微信页面，然后微信会跳转到我们设定的 `redirect_uri` 上，
并且通过 `$route.query.code` 来返回 `code`
2. 通过 `code` 换取网页授权 `access_token`。通过 `access_token` 拉取用户信息。
这步是后端来实现的，所以前端只要通过调用后端接口就行了。😊

用户信息包含：城市，省，国家，头像，昵称，性别，openid

### `js-sdk` 之自定义分享

> 微信JS-SDK是微信公众平台 面向网页开发者提供的基于微信内的网页开发工具包。

#### js-sdk使用步骤

1. 引入js文件：（支持https）：http://res.wx.qq.com/open/js/jweixin-1.4.0.js
2. 注入权限验证配置

> 所有需要使用JS-SDK的页面必须先注入配置信息，否则将无法调用（同一个url仅需调用一次，对于变化url的SPA的web app可在每次url变化时进行调用)

```js
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [] // 必填，需要使用的JS接口列表
});
```

这些数据都可以让后端生成，😊。

这里存在一个坑。对于android设备，vue项目一切正常（可能是和官网说的修复有关）。

**对于ios设备，vue项目的URL是不会变化的，只对第一次进入的URL能验证成功。**
当然页面fresh，是会刷新的url的。这个说的不会变化是指 `$router.push` 方法。

3. 通过ready接口处理成功验证
4. 自定义分享内容
```js
wx.ready(function () {   //需在用户可能点击分享按钮前就先调用
  wx.updateAppMessageShareData({ // 分享到朋友
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    success: function () {
      // 设置成功
    }
  })
  wx.updateTimelineShareData({ // 分享到朋友圈
    title: '', // 分享标题
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    success: function () {
      // 设置成功
    }
  })
});

```

##### vue项目

因为上面说的那个坑，所以在vue项目中，需要做一些特殊处理。

处理原理是，在最开始进入页面的时候纪录下url，然后在每次路由刷新的时候判断是`andriod`机还是`ios`机子，做不同的处理.

下面给出具体代码

```js
// router.js
import { wechatAuth } from '@/utils/wechatConfig.js'

router.afterEach(to => {
  let authUrl = `${window.location.origin}${to.fullPath}`

  if (window.__wxjs_is_wkwebview) {
    // IOS
    if (!window.entryUrl) {
      window.entryUrl = authUrl // 将后面的参数去除
    }
    wechatAuth(authUrl, 'ios', to.meta.title)
  } else {
    // 安卓
    setTimeout(function() {
      wechatAuth(authUrl, 'android', to.meta.title)
    }, 500)
  }
})
```

```js
// wechatAuth
import api from '@/api'
import '@/lib/jweixin-1.4.0.js'

let indexOfConfig = 0

const updateShareData = (shareConfig, shareSuccessCallback) => {
  wx.updateAppMessageShareData({
    title: shareConfig.title,
    desc: shareConfig.desc,
    link: shareConfig.link,
    imgUrl: shareConfig.imgUrl,
    success: function() {
      shareSuccessCallback()
    }
  })
  wx.updateTimelineShareData({
    title: shareConfig.title,
    link: shareConfig.link,
    imgUrl: shareConfig.imgUrl,
    success: function() {
      shareSuccessCallback()
    },
    fail: function() {}
  })
  wx.onMenuShareTimeline({
    title: shareConfig.title,
    link: shareConfig.link,
    imgUrl: shareConfig.imgUrl,
    success: function() {
      shareSuccessCallback()
    }
  })
  wx.onMenuShareAppMessage({
    title: shareConfig.title,
    desc: shareConfig.desc,
    link: shareConfig.link,
    imgUrl: shareConfig.imgUrl,
    success: function() {
      shareSuccessCallback()
    }
  })
}

export const wechatAuth = async (authUrl, device, title, allowShare = true, shareSuccessCallback = () => {}) => {
  const shareConfig = {
    title: 'title',
    desc: 'desc',
    link: allowShare ? authUrl : window.location.origin,
    imgUrl: window.location.origin + '/favicon-matpool.ico'
  }

  const params = {
    // 这里在别人的代码中调用了encodeURIComponent，但我测试的结果是：没有encodeURIComponent才能正确分享。
    url: device === 'ios' ? window.entryUrl : authUrl 
  }
  const authRes = await api.getWxConfig(params)

  if (authRes && wx) {
    if (device !== 'ios' || indexOfConfig === 0) {
      indexOfConfig++

      wx.config({
        debug: false,
        appId: authRes.app_id,
        timestamp: authRes.timestamp,
        nonceStr: authRes.nonce,
        signature: authRes.signature,
        jsApiList: [
          'updateAppMessageShareData',
          'updateTimelineShareData',
          'onMenuShareAppMessage',
          'onMenuShareTimeline'
        ]
      })

      wx.ready(() => {
        updateShareData(shareConfig, shareSuccessCallback)
      })
    } else {
      updateShareData(shareConfig, shareSuccessCallback)
    }
  }
}

```

**NOTE**：就是这个encodeURIComponent坑了我一天的时间。😅

## 参考

1. [微信公众平台技术文档](https://mp.weixin.qq.com/wiki)
2. [微信web开发者工具](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1455784140)
3. [微信js-sdk](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)
4. [微信网页开发样式库](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1455784134)
