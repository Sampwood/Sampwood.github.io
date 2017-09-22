---
title: git本地存储github账户信息
date: 2017-09-22 12:46:00
categories:
- coding
- version-control
tags: git
---
### git配置信息由config来维护

1. system级别：/%GitPath%/mingw64/etc/gitconfig文件

2. global级别：%home%/.gitconfig文件

3. local级别：%ProjectPath%/.git/config文件

这些文件指定了github账户在本地的存储方式。配置生效的优先级：local>global>system。[credential]表示信息存储方式
<!--more-->
### 查看配置

`git config --system/global/local -l`

### 使用http协议链接github

github支持http和ssh协议连接。使用http协议连接时需要输入github账户和密码。git在本地有多种方式来存储本地凭证。

存储方式有三种：mananger、wincred和store，可以在/%GitPath%/mingw64/libexec/git-core路径下找到对应的exe文件：git-credential-manager、git-credential-wincred和git-credential-store。

通过配置config文件中的[credential]结点下的helper参数，Git可以指定辅助工具来存储本地凭证。例如：

> [credential]
>    helper = manager

### manager

此种方式是把凭证存储至Windows的凭据管理器中：

![manager-git](/img/manager-git.jpg)

### wincred

此种方式也是把凭证存储至Windows的凭据管理器中（不清楚和上面的manager的区别是什么？TODO）

### store

此种方式将账户信息保存至%home%目录下的.git-credentials文件，并且以明文存储，内容如下：

> https://username:password@github.com

这种方式也可以把信息存储在指定的文件里面。



## 参考

1. http://www.jianshu.com/p/0ad3d88c51f4