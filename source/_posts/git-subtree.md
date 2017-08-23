---
title: git subtree
date: 2017-07-18 18:38:53
categories:
- coding
- version-control
tags: git
---
在git项目中，如果要依赖另一个git项目，可以使用git subtree来分开维护两个项目。

## usage

### 添加子目录，并与git项目建立关联

```
git remote add -f <子仓库名> <子仓库地址>
git subtree add --prefix=<子目录名> <子仓库名> <分支> --squash
```
<!--more-->
### 从远程仓库更新子目录

```
git fetch <远程仓库名> <分支>
git subtree pull --prefix=<子目录名> <远程分支> <分支> --squash
```

### 从子目录push到远程仓库

```
git subtree push --prefix=<子目录名> <远程分支名> 分支
```