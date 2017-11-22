---
title: github集成---Travis CI和Coveralls
date: 2017-09-22 13:02:07
categories:
- coding
- version-control
tags: git
---
项目提交github之后可以集成一些在线工具，例如Travis CI、Coveralls等。Travis CI可以在每次github有新的改动的时候获取到最新的代码，然后构建，编译以及跑单元测试，确保代码正确性。Coveralls可以从Travis CI跑的结果统计出单元测试对代码的覆盖率。

### Travis CI

首先在项目中用mocha+chai来写单元测试。

> npm i mocha -D
> npm i chai -D

写一些单元测试...
<!--more-->
在package.json中加入下面代码：

```json
"scripts": {
    "test": "mocha --reporter spec"
}
```

然后在命令行运行 `npm test` 来跑单元测试，Travis CI也是默认运行 `npm test`。

其次用github授权登录Travis CI，把要集成CI的项目左边的按钮点亮。

在项目根目录中新建.travis.yml文件，写入（具体写法可查看Travis CI的文档）：
```
language: node_js
node_js:
    - stable
```

默认参数如：`install: - npm install` 和 `script:- npm test` 可不写。

这样子就完成了设置，下次github仓库有代码提交之后就会跑CI：

![Travis CI结果图](/images/Travis_CI.png)

如果想在github项目的markdown文件中直接看到CI结果，可以点击上图中【build|passing】按钮，然后出现的dialog中的第二个下拉按钮选markdown，然后拷贝下面文本框内容，粘贴到项目的md文件中。

![CI和coverage直观结果](/images/coverage_CI.png)

### Coveralls

在项目中用Istanbul+Coveralls来完成覆盖率的统计和输出：

> npm i coveralls -D
> npm i istanbul -D

在package.json中加入下面代码：

```
"scripts": {
    "cover": "istanbul cover --report lcov node_modules/mocha/bin/_mocha -- -R spec test/*.js"
}
```

更新.travis.yml文件：
```
language: node_js
node_js:
    - stable
script:
    - npm run cover
after_script: "cat coverage/lcov.info | node_modules/coveralls/bin/coveralls.js"
```

其次用github授权登录Coveralls，把要集成CI的项目左边的按钮点亮。

这样子就完成了设置，下次Travis CI跑完之后，就会在Coveralls上统计覆盖率信息。

同理在Coveralls里，单击【BADGE】右上角的按钮，拷贝markdown里面内容到md文件中。