---
title: npm scripts设置环境变量方法
categories:
  - coding
  - js
tags:
  - environment
  - linux
  - npm
date: 2019-04-01 08:59:06
update: 2019-04-01 08:59:06
---

在 `package.json` 中，如果希望在 `script` 属性的运行脚本中设置环境变量，来区分开发环境 或 生产环境。
unix与windows环境的设置方式是不同的。
这个不同可以通过`cross-env`来实现跨平台设置。

<!-- more -->

windows: 
```
set NODE_ENV=production

"scripts": {
    "build": "set NODE_ENV=production && npm run build",
    "dev": "set NODE_ENV=development && npm run dev",
}
```

linux & mac:
```
export NODE_ENV=production

"scripts": {
    "build": "NODE_ENV=production npm run build",
    "dev": "NODE_ENV=development npm run dev",
}
```

cross-env跨平台设置：
```
npm i cross-env -D

"scripts": {
    "build": "cross-env NODE_ENV=production npm run build",
    "dev": "cross-env NODE_ENV=development npm run dev",
}
```

## 参考
1. [npm scripts设置环境变量方法](https://www.cnblogs.com/mengff/p/7350030.html)
2. [package.json里script设置环境变量，unix与windows的区别](https://www.jianshu.com/p/60c4ce9111c0)
