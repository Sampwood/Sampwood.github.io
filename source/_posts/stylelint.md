---
title: 引入stylelint对项目做代码样式审查
date: 2019-10-09 11:05:09
categories:
- coding
- css
tags: 
- css
- environment
---

早就看到`stylelint` 的介绍文章，感觉有点意思。因为懒，这几天才接入了这个样式检查的工具。

话不多说，直接看如何引入这个库吧。

### install

安装相关依赖库
```bash
yarn add stylelint stylelint-config-standard stylelint-config-prettier
```

<!-- more -->

从这里看好像并没有安装`webpack`相关的`plugin`啊，难道集成到`webpack`中不是更方便吗？

这里就不得不说下了，在[Vue Loader-代码校验 (Linting)](https://vue-loader.vuejs.org/zh/guide/linting.html#eslint)中有介绍如何集成
`stylelint` 到 `webpack`，做自动化检测。

但是这个配置其实不适用在由`vue cli3`生成的项目中。配置`stylelint-webpack-plugin`插件到`vue.config.js`中之后，项目运行不起来。😣

### usage

#### 规则文件

新建规则文件：`.stylelintrc.js`
```javascript
module.exports = {
  extends: [
    'stylelint-config-standard',
    'stylelint-config-prettier'
  ],
  rules: {
    'rule-empty-line-before': null,
    'at-rule-empty-line-before': null,
    'no-descending-specificity': null,
    'font-family-no-missing-generic-family-keyword': null,
    'max-empty-lines': 1,
    'block-opening-brace-space-before': 'always',
    'indentation': [2],
    'function-comma-space-after': 'always-single-line',
    'selector-list-comma-newline-after': 'always',
    'value-list-comma-newline-after': 'always-multi-line',
    'declaration-block-trailing-semicolon': 'always',
    'declaration-colon-space-after': 'always',
    'color-hex-case': 'lower'
  }
}
```

因为我在项目用了`prettier` 来格式化js代码，为了保证其与`stylelint` 不冲突，我`extends`了`stylelint-config-prettier`。
这样我就不得不加一些自定义的`rules`了。因为`stylelint-config-prettier`的规则真的很弱，很多都不检查。

当然如果要忽略某些文件，可以建一个`.stylelintignore`文件。

#### package.json

在`package.json` 中定义`script`来自动运行监测。

```json
{
  "scripts": {
    "lint": "vue-cli-service lint", // vue cli3生产项目自带的
    "lint:style": "stylelint 'public/index.html' 'src/**/*.{css,less,vue}'", //新加的
  },
  "lint-staged": {
    "*.{js,vue}": [ // 这个是prettier用的，如果没有在项目用prettier，就不需要这个
      "vue-cli-service lint",
      "git add"
    ],
    "*.{html,vue,css,less}": [ // 这个是stylelint用的
      "stylelint 'public/index.html' 'src/**/*.{css,less,vue}' --fix",
      "git add"
    ]
  },
}
```

这样配置`package.json`文件之后，就可以手动运行`yarn lint:style`来检查样式代码了，或者`yarn lint:style --fix`来自动矫正

在`lint-staged`中加的代码，是为了能在代码`git commit`之前自动做一个检查，也就不需要每次都手动去跑命令了。

### vscode工具

在`vscode`中安装`stylelint`插件，按照他的要求修改下`settings.json`

```json
"css.validate": false,
"less.validate": false,
"scss.validate": false
```

但是这个插件目前还不支持在保存时自动fix。`stylelint-plus`可以，但是下载量还是较少

So，到此，引入`stylelint`成功。👏👏👏

## 参考
1. [stylelint官网](https://stylelint.io/)
2. [prettier-Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html#recommended-configuration-2)
3. [Vue Loader-代码校验 (Linting)](https://vue-loader.vuejs.org/zh/guide/linting.html#eslint)
3. [引入stylelint对CSS/Sass做代码审查](https://segmentfault.com/a/1190000008708473)
4. [如何为你的 Vue 项目添加配置 Stylelint](https://juejin.im/post/5c31c9a16fb9a049f8197000)
5. [在Vue项目中使用Eslint+Prettier+Stylelint](https://segmentfault.com/a/1190000020168436)