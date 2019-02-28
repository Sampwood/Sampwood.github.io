---
title: 全局配置ESLint
categories:
  - coding
  - linter
tags:
  - environment
  - eslint
date: 2019-01-04 14:23:58
update: 2019-01-04 14:23:58
---

> ESLint是一个Javascript静态检查工具，它可以帮你养成良好的编程习惯，使你的javascript代码达到国际化的水准。ESLint是所有Javascrpt静态检查工具里最晚诞生的一个，之前还曾经有过JSLint以及JSHint等工具，但JSLint和JSHint都是用的一套标准，在目前这个前端技术飞速发展的时代已经显得有点落伍。ESLint的好处是既提供了国际大厂的标准，同时又给了你自定义标准的可能性。ESLint的推荐设置方式是按项目设置，但是如果我们有很多个不同的javascript项目的话，一个一个去设置未免太麻烦，所以在这里介绍的是全局设置方法，一次设置，所有项目全部采用同一标准。

目前在自己的机子上面用到的eslint配置是：`airbnb`+`eslint-plugin-vue`。

<!-- more -->

### 安装

需要安装的包有：`eslint`, `eslint-config-airbnb-base`, `eslint-plugin-import`, `eslint-plugin-vue`。
```
npm i -g eslint eslint-config-airbnb-base eslint-plugin-import eslint-plugin-vue
```

### 配置文件

安装好依赖包之后，需要编写全局`.eslintrc`文件：
```
{
  "extends": [
    "airbnb-base",
    "plugin:vue/recommended",
  ],
}
```

如此这般，就可以在任何地方js文件中使用同一套标准去检查了。


## 参考
1. [【第1495期】 ESLint 工作原理探讨](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651230875&idx=1&sn=092211db96adfc85a26b457f7e9421a0&chksm=bd494b1f8a3ec20902ad0df7d6a3735b536fe585086abc9035fe24d69549bb4c81cf88658515&mpshare=1&scene=1&srcid=0104C1J16lllbEgkIyFIFJmp#rd)
1. [atom使用全局配置ESLint](https://segmentfault.com/a/1190000005984309)
1. [airbnb/javascript](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb)
1. [eslint-plugin-vue](https://vuejs.github.io/eslint-plugin-vue/user-guide/)
1. [dustinspecker/awesome-eslint](https://github.com/dustinspecker/awesome-eslint)