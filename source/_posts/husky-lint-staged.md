---
title: 代码检查工作流之husky+lint-staged
categories:
  - coding
  - linter
tags:
  - environment
  - eslint
date: 2019-02-28 14:13:09
update: 2019-02-28 14:13:09
---


虽然大家都会在项目中使用eslint等工具来进行代码格式检查。
但如果每次都手动去跑`npm run lint`是一件很麻烦的事情，而且很容易忘记。

那么，有没有办法解决这个问题呢？

在这里我用这个办法：在`git commit`的时候来自动跑eslint来检查代码。

需要的工具是： `husky` + `lint-staged`

`husky`的作用是提供`git commit hook`，`lint-staged`的作用是让eslint的范围限定在本次的改动文件中。

<!-- more -->

### 配置`husky`+`lint-staged`

安装插件：
```
npm install -D husky lint-staged
```

修改`package.json`文件：
```
{
  "scripts": {
    "precommit": "lint-staged"
  },
  "lint-staged": {
    "src/**/*.js": "eslint"
  }
}

```



## 参考
1. [用 husky 和 lint-staged 构建超溜的代码检查工作流](https://segmentfault.com/a/1190000009546913)