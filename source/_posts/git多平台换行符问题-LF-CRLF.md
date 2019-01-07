---
title: git多平台换行符问题(LF/CRLF)
categories:
  - coding
  - version-control
tags:
  - git
date: 2019-01-07 16:51:29
update: 2019-01-07 16:51:29
---

### 差异

目前大部分的前端开发用macos/windows来开发，linux系统使用的较少。
一个多人开发的项目必然会覆盖windows和unix系统。
而这个两个系统间存在一些差异，其中之一就是文本换行的方式。

CRLF, LF 都是用来表示文本换行的方式。主流的操作系统一般使用CRLF或者LF作为其文本的换行符。
CR(Carriage Return) 代表回车，对应字符 `\r`；LF(Line Feed) 代表换行，对应字符 `\n`。

unix/linux(macos)使用的是LF，windows使用的是CRLF。

跨平台协作开发是常有的，不统一的换行符确实对跨平台的文件交换带来了麻烦。
最大的问题是，在不同平台上，换行符发生改变时，Git 会认为整个文件被修改，这就造成我们 `diff` 不能正确反映本次的修改。

例如在查看`diff`的时候，出现下面的情况：
```
warning: LF will be replaced by CRLF in .gitignore.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in README.md.
The file will have its original line endings in your working directory
...
```

<!-- more -->

### git处理

还好 Git 在设计时就考虑了这一点，提供了换行符相关的处理方式。

这里先指定两个非官方的概念，方便后面解释与描述：(重要，否则后面看不懂)

1. **标准化** 指在提交代码到git数据库(本地库) 中将文本文件中的换行符CRLF转为LF的过程
2. **转换** 指在检出Git数据库代码过程中将文本文件中的换行符LF转换为CRLF的过程

#### core.autocrlf

git提供了一个 `autocrlf` 的配置项，用于在提交和检出时自动转换换行符，该配置有三个可选项：

- true: 提交时转换为 LF，检出时转换为 CRLF
- false: 提交检出均不转换
- input: 提交时转换为LF，检出时不转换

> CRLF 与 LF 混合的文本文件不受此配置控制。

> Git 安装后默认为 false

所以，一种规范换行符的方式是这样的：

使用 Windows 系统的开发者设置：
```
git config --global core.aurocrlf true
```

使用 Linux/MacOS 的开发者设置：
```
git config --global core.autocrlf input
```

#### core.safecrlf

git同时提供了另一个配置项 `safecrlf`，用于检查文件是否包含混合换行符，该配置也有三个可选项：

- true 禁止提交混合换行符的文本文件(git add 的时候会被拦截，提示异常)
- warn 提交混合换行符的文本文件的时候发出警告，但是不会阻止 git add 操作
- false 不禁止提交混合换行符的文本文件（默认配置）

该配置用来防止错误的标准化与转换。

#### `.gitattributes` 文件

`core.autocrlf` 的配置依赖于每一位参与项目的开发机器上的配置，这很难确保每个人都能正确配置。
于是在规范项目中的换行符方面，还有一套添加配置文件的方案。
在项目的根目录下可以添加一个`.gitattributes` 文件。
它的优先级高于`core.autocrlf`的设置，可以覆盖`core.autocrlf`的。
它类似于 `.gitignore` 文件，随提交修改生效，一个项目中可以维持一份相同的配置。
所以，它能够避免每个开发人员配置不同的问题。

`.gitattributes`文件的功能不只有配置换行符，所以它的配置相对复杂一下。
详细的说明文档可以参考 [地址](http://schacon.github.io/git/gitattributes.html)。
这里只针对换行符的配置做一下简单的介绍：

每行基本形式：

```
filter attr1 attr2 ....
```

filter 代表匹配文件的通配符，在它后面跟着相应的属性，用空格间隔。

filter 的选项比较简单，常见的:

```
* 匹配所有文件
*.txt  匹配文件名以txt结尾的文件
```

attr的选择比较多，其中与换行符相关的属性只有几条：

- text
    - text 自动完成标准化与转换
    - -text 不执行标准化与转换
    - text=auto 根据 Git 决定是否需要执行标准化与转化
    - 不设置 使用core.autocrlf配置决定是否执行标准化与转换
- eol
    - eol=lf 强制完成标准化，不执行转换（相当于指定转换为LF格式）
    - eol=crlf 强制完成标准化，指定转换为CRLF格式
- binary
    - binary 二进制文件不参与标准化与转换
    - 不设置 由 Git 决定是否为二进制文件

> text 设置的时候，转换自动转换到对应平台的换行符

> 行号高的设置会覆盖行号低的设置

这里给出一个简单的例子来说明一下：
```
# Set the default behavior, in case people don't have core.autocrlf set.
* text eol=lf

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
```

**推荐使用.gitattributes来规范项目中换行符。简单，方便，灵活。**

## 参考
1. [理解 CRLF，LF](https://www.jianshu.com/p/ec9564fe1c2b)
2. [Git 多平台换行符问题(LF or CRLF)](http://blog.konghy.cn/2017/03/19/git-lf-or-crlf/)

