---
title: git subtree
date: 2017-07-18 18:38:53
update: 2018-09-30 16:26:53
categories:
- coding
- version-control
tags:
- git
---
在git项目中，如果要依赖另一个git项目，可以使用git subtree来分开维护两个项目。

### usage

#### 添加子目录，并与git项目建立关联

```
git remote add -f <子仓库名> <子仓库地址>
git subtree add --prefix=<子目录名> <子仓库名> <分支>
```
<!--more-->

~~**Note**：在我这，如果用`--squash`加入子目录，在更新子项目时`git subtree pull`会出现~~
~~`fatal: refusing to merge unrelated histories`错误。~~

**Note**：如果用`--squash`加入子目录，在更新子项目时也要加上`--squash`

#### 从远程仓库更新子目录

```
git fetch <远程仓库名> <分支>
git subtree pull --prefix=<子目录名> <远程分支> <分支>
```

#### 从子目录push到远程仓库

```
git subtree push --prefix=<子目录名> <远程分支名> 分支
```

### 高阶功能

重新split出一个新起点（这样，每次提交subtree的时候就不会从头遍历一遍了），
同时创建出一个只包含子项目的分支，可以在这个分支上直接提交到子项目，就不用`git subtree push`：
```
git subtree split --rejoin --prefix=components/zenjs --branch new_child
git push child new_child:master
```

`--rejoin`用来生成锚点，`--branch`用来生成子项目分支