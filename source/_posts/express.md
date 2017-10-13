---
title: express学习笔记
date: 2017-10-12 10:17:17
categories:
- coding
- web framework
tags: express
toc: true
---

基于 Node.js 平台，快速、开放、极简的 web 开发框架。

`$ npm install express --save`

<!--more-->
### session

> express-session  
在Express4.x版本之后，session和cookies等模块不再包含在Express框架中，需要单独添加相应模块。

`$ npm install express-session --save`

    var session = require('express-session');
    var app = express();
    
    // ...
    
    app.use(session({
        // name: 设置 cookie 中，保存 session 的字段名称，默认为 connect.sid 。
        // store: session 的存储方式，默认存放在内存中，也可以使用 redis，mongodb 等。Express 生态中都有相应模块的支持。
        // secret: 通过设置的 secret 字符串，来计算 hash 值并放在 cookie 中，使产生的 signedCookie 防篡改。
        // cookie: 设置存放 session id 的 cookie 的相关选项，默认为(default: { path: '/', httpOnly: true, secure: false, maxAge: null })
        // genid: 产生一个新的 session_id 时，所使用的函数， 默认使用 uid2 这个 npm 包。
        // rolling: 每个请求都重新设置一个 cookie，默认为 false。
        // resave: 即使 session 没有被修改，也保存 session 值，默认为 true。
    }));
    
例子：
    
    app.use(session({ secret: 'keyboard cat', cookie: { maxAge: 60000 }}))

    app.use(function(req, res, next) {
      var sess = req.session
      if (sess.views) {
        sess.views++
        res.setHeader('Content-Type', 'text/html')
        res.write('<p>views: ' + sess.views + '</p>')
        res.write('<p>expires in: ' + (sess.cookie.maxAge / 1000) + 's</p>')
        res.end()
      } else {
        sess.views = 1
        res.end('welcome to the session demo. refresh!')
      }
    })
    
### 中间件

> 利用 Express 中间件功能实现登录拦截。如果用户请求的路径需要登录后才能访问，将用户重定向到登录页面，登录成功后将用户重定向到原始请求路径。

### 配置

- 访问刷新session计时：session({rolling: true})