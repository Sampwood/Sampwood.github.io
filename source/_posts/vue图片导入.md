---
title: vue图片导入
categories:
  - coding
  - vue
tags:
  - vue
  - 文件引用路径
date: 2018-01-24 15:12:11
update: 2018-01-24 15:12:11
---
### vue 中引入图片

### 当前webpack配置

当前的webpack.base.conf.js：
```
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
    'vue$': 'vue/dist/vue.esm.js',
    '@': resolve('src')
  }
}
```
<!-- more -->

#### 静态路径

##### `~`
模板代码为：`<img class="logo" src="~assets/logo.png">`

编译报错:
```
ERROR  Failed to compile with 1 errors  

This dependency was not found:

* assets/logo.png in ./~/vue-loader/lib/template-compiler?{"id":"data-v-5e23f6c6","hasScoped":true,"transformToRequire":{"video":"src","source":"src","img":"src","image":"xlink:href"},"buble":{"transforms":{}}}!./~/vue-loader/lib/selector.js?type=template&index=0&bustCache!./src/components/main/card.vue

To install it, you can run: npm install --save assets/logo.png
```

##### `..`
模板代码为：`<img class="logo" src="../../assets/logo.png">`

**成功**

##### `@`
模板代码为：`<img class="logo" src="@/assets/logo.png">`

编译成功，页面报错:
```
GET http://localhost:8080/@/assets/logo.png 404 (Not Found)
```

##### `~@`
模板代码为：`<img class="logo" src="~@/assets/logo.png">`

**成功**


#### 动态路径

##### `~`
模板代码为：
```
<img class="logo" :src="attachmentUrl">

data () {
  return {
    attachmentUrl: require('~assets/logo.png')
  } 
}
```

编译报错：
```
ERROR  Failed to compile with 1 errors 

This dependency was not found:

* ~assets/logo.png in ./~/babel-loader/lib!./~/vue-loader/lib/selector.js?type=script&index=0&bustCache!./src/components/main/card.vue

To install it, you can run: npm install --save ~assets/logo.png
```

##### `..`
模板代码为：
```
<img class="logo" :src="attachmentUrl">

data () {
  return {
    attachmentUrl: require('../../assets/logo.png')
  } 
}
```

**成功**

##### `@`
模板代码为：
```
<img class="logo" :src="attachmentUrl">

data () {
  return {
    attachmentUrl: require('@/assets/logo.png')
  } 
}
```

**成功**

##### `~@`
模板代码为：

```
<img class="logo" :src="attachmentUrl">

data () {
  return {
    attachmentUrl: require('~@/assets/logo.png')
  } 
}
```

编译报错：
```
ERROR  Failed to compile with 1 errors 

This dependency was not found:

* ~@/assets/logo.png in ./~/babel-loader/lib!./~/vue-loader/lib/selector.js?type=script&index=0&bustCache!./src/components/main/card.vue

To install it, you can run: npm install --save ~@/assets/logo.png
```
