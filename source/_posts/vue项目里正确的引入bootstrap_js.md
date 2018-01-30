---
title: vue项目里正确的引入bootstrap.js
categories:
  - coding
  - vue
tags:
  - vue
  - 第三方库引用
date: 2018-01-29 14:30:11
update: 2018-01-29 14:41:11
---

### 正确的引入依赖库(jQuery, popper.js)

对于使用vue-cli构建的vue项目，需要修改`build/webpack.base.conf.js`文件，修改两处代码
<!-- more -->

```
module.exports = {
  //other code ...

  resolve: {
    extensions: ['', '.js', '.vue'],
    fallback: [path.join(__dirname, '../node_modules')],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      
      // webpack 使用 jQuery，如果是自行下载的
      // 'jquery': path.resolve(__dirname, '../src/assets/libs/jquery/jquery.min'),
      // 如果使用NPM安装的jQuery
      'jquery': 'jquery',
      'popper': 'popper.js'
    }
  },

  // ...
  // 增加一个plugin
  plugins: [
    new webpack.ProvidePlugin({
      '$': 'jquery',
      'jQuery': 'jquery',
      'Popper': 'popper'
    })
  ]
```

参数`alias`是用来指明路径，上面就指明了`jquery`,`popper`分别指向用`npm`下载下来的`jquery`和`popper.js`第三方库。

参数`plugins`则是定义全局变量。`'Popper': 'popper'`后面的`popper`就是`alias`中定义的路径。

这样就是可以正确的引用`bootstrap.js`了：
```
import 'bootstrap/dist/js/bootstrap.js'
```

