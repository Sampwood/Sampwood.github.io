---
title: project 基础设置
categories:
  - coding
tags:
  - environment
date: 2019-05-15 20:59:36
update: 2019-05-15 20:59:36
---

在开发项目的时候，我们要设置一些基础配置，让不同开发人员在不同环境下能写出统一规范的代码。
下面就记录下本人在使用的一些通用设置。

### eslint + prettier

`eslint` 用来进行代码的校验，`prettier` 用于统一代码风格

`eslint` 的安装和使用在之前的一篇文章中已经说明。下面介绍下 `prettier`:

```bash
npm i -D prettier eslint-plugin-prettier eslint-config-prettier
```

<!-- more -->

`eslint-plugin-prettier` 插件会调用 `prettier` 对你的代码风格进行检查.
其原理是先使用 `prettier` 对你的代码进行格式化，然后与格式化之前的代码进行对比，如果过出现了不一致，这个地方就会被 `prettier` 进行标记。
 
通过使用 `eslint-config-prettier` 配置，能够关闭一些不必要的或者是与 `prettier` 冲突的lint选项。
这样我们就不会看到一些 `error` 同时出现两次。使用的时候需要确保，这个配置在 `extends` 的最后一项。

```js
//.eslintrc.js
{
  extends: [
    'standard', //使用standard做代码规范
    // https://github.com/vuejs/eslint-plugin-vue#priority-a-essential-error-prevention
    // consider switching to `plugin:vue/strongly-recommended` or `plugin:vue/recommended` for stricter rules.
    'plugin:vue/essential',
    'plugin:prettier/recommended',
  ],
  plugins: ['prettier'],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'prettier/prettier': [
      'error',
      { // prettier 配置
        semi: false,
        singleQuote: true,
        printWidth: 120
      }
    ]
  },
}
```

**NOTE: 配置方法有好几种，本人在 `.eslintrc.js` 中对 `prettier` 进行配置**

### husky + lint-staged

这个在之前的文章中有介绍。

当然，现在 `husky` 在 `package.json` 提供了额外的配置参数 `husky`，而不是在 `script` 中写 `precommit`

### Commitizen + Commitlint

用这两个插件来让你的 git commit message 更加的规范和统一。
`Commitizen` 是一套模版化的commit message, `Commitlint` 用来检查commit message 是否符合制定模板

```bash
npm install -D commitizen cz-customizable
npm install -D commitlint-config-cz @commitlint/cli @commitlint/config-conventional
```

结合 `husky` 插件就能拥有同样的 `git commit` 命令，不一样的体验，而不是使用 `git cz`命令

```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "prepare-commit-msg": "exec < /dev/tty && git cz --hook",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  },
  "lint-staged": {
    "src/**/*.{js,vue}": [
      "eslint --fix",
      "git add"
    ]
  },
  "config": {
    "commitizen": {
      "path": "cz-customizable"
    }
  }
}
```

额外需要增加下面这些文件：

```js
// .cz-config.js
'use strict';

module.exports = {

  types: [
    {
      value: 'WIP',
      name : '💪  WIP:      Work in progress'
    },
    {
      value: 'feat',
      name : '✨  feat:     A new feature'
    },
    {
      value: 'fix',
      name : '🐞  fix:      A bug fix'
    },
    {
      value: 'refactor',
      name : '🛠  refactor: A code change that neither fixes a bug nor adds a feature'
    },
    {
      value: 'docs',
      name : '📚  docs:     Documentation only changes'
    },
    {
      value: 'test',
      name : '🏁  test:     Add missing tests or correcting existing tests'
    },
    {
      value: 'chore',
      name : '🗯  chore:    Changes that don\'t modify src or test files. Such as updating build tasks, package manager'
    },
    {
      value: 'style',
      name : '💅  style:    Code Style, Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)'
    },
    {
      value: 'revert',
      name : '⏪  revert:   Revert to a commit'
    }
  ],

  scopes: [],

  allowCustomScopes: true,
  allowBreakingChanges: ["feat", "fix"]
};

```

```js
// .commitlintrc.js
module.exports = {
  extends: ['@commitlint/config-conventional', 'cz'],
  rules: {
    'type-enum': [2, 'always', ['WIP', 'feat', 'fix', 'refactor', 'docs', 'test', 'chore', 'style', 'revert']],
    'type-case': [0, 'never']
  }
}   
```

### standard-version

如果使用上面的插件来规范我们的git commit message，
那么我们就可以借助 standard-version 这样的工具, 自动生成 CHANGELOG, 甚至是 语义化的版本号(Semantic Version).

```bash
npm i -D standard-version
```

在 `package.json` 中配置：

```json
{
  "scirpt": {
    ...,
    "release": "HUSKY_SKIP_HOOKS=1 standard-version"
  }
}
```

**Note**: `HUSKY_SKIP_HOOKS=1` 用于取消husky中第二个hook（`prepare-commit-msg`）,这个需要`husky`升级到`3.0.0`版本以上


## 参考

1. [使用ESLint+Prettier来统一前端代码风格](https://juejin.im/post/5b27a326e51d45588a7dac57)
2. [优雅的提交你的 Git Commit Message](https://juejin.im/post/5afc5242f265da0b7f44bee4#heading-7)
