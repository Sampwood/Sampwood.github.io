---
title: 记一次webpack debug
categories:
  - coding
  - js
tags:
  - webpack
date: 2020-08-09 22:01:33
update: 2020-08-09 22:01:33
---

项目需要引入jupyterlab组件，结果在项目中导入css出错了，报错信息是找不到 `@jupyterlab/application/style/index.css` 文件中的依赖样式文件 `@import url('~@fortawesome/fontawesome-free/css/all.min.css');`

<!-- more -->

报错信息如下:
```
error  in ./src/views/jupyterlab/index.vue?vue&type=style&index=1&lang=css&

Module build failed (from ./node_modules/postcss-loader/lib/index.js):
Error: Failed to find '~@fortawesome/fontawesome-free/css/all.min.css'
in [
  /Users/sam/Documents/code/AICloud_Web/node_modules/@jupyterlab/application/style
]
  at resolveModule.catch.catch (/Users/sam/Documents/code/AICloud_Web/node_modules/postcss-import/lib/resolve-id.js:35:13)

@ ./node_modules/vue-style-loader!./node_modules/css-loader??ref--6-1!./node_modules/vue-loader/lib/loaders/stylePostLoader.js!./node_modules/postcss-loader/lib??ref--6-2!./node_modules/vue-loader/lib??vue-loader-options!./src/views/jupyterlab/index.vue?vue&type=style&index=1&lang=css& 4:14-313 14:3-18:5 15:22-321
@ ./src/views/jupyterlab/index.vue?vue&type=style&index=1&lang=css&
@ ./src/views/jupyterlab/index.vue
@ ./src/router/index.js
@ ./src/main.js
```

哎，由于经验不足，查了半天才发现原因。其实从错误信息中`Module build failed (from ./node_modules/postcss-loader/lib/index.js)` 可以看到是 postcss-loader 找不到`~@fortawesome/fontawesome-free/css/all.min.css`，从 `at resolveModule.catch.catch (/Users/sam/Documents/code/AICloud_Web/node_modules/postcss-import/lib/resolve-id.js:35:13)` 可以看到 postcss-loader 是用了 `postcss-import` 来引入依赖的样式文件的。如果能理解这个错误信息，那么debug过程就顺利很多。

正常来说 `~` 是用来表明后面的内容是第三方依赖的文件，而 `postcss-import` 却[不支持这个语法](https://github.com/postcss/postcss-import/issues/386#issuecomment-449740987)。。。

既然知道是postcss的问题，那么再来看下项目中postcss的配置文件。

```
module.exports = {
  "plugins": {
    "postcss-import": {},
    "postcss-url": {},
    // to edit target browsers: use "browserslist" field in package.json
    "autoprefixer": {}
  }
}
```

从配置文件中可以看到 postcss-import 是在这里引入的，那么只要把 `postcss-import` 和 `
postcss-url` 删掉就好了，事实也确实如此！

当然还要检查下之前的样式引入是否存在问题。因为 **`postcss-import` 是直接使用包名就能找到依赖项的，而 `css-loader` 需要在包名前加上 `~`**。