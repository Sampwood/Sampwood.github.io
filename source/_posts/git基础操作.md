---
title: git基础操作
date: 2017-07-24 19:14:46
categories:
- coding
- version-control
tags: git
---
记录一些git操作

### fetch

用于获取远程仓库代码:

```
git fetch <远程主机名> <分支名>
```

fetch下来的代码，在本地主机上可以用`远程主机名/分支名`的形式读取。
<!--more-->
把远程remoteBranch分支拉去到本地的localBranch下，没有则创建: 
```
git fetch origin localBranch:remoteBranch
```

### checkout

用于切换分支 

获取远程分支remoteBranch 到本地新分支localBranch，并跳到localBranch分支:
```
git checkout origin/remoteBranch -b localBranch
```

删除本地有所有改动：
```
git checkout -f
```

### stash

用于保存本地修改并还原到干净的工作分支。

基础用法
```
git stash 保存所有修改
git stash list 查看所有保存列表
git stash pop 从所有保存的中推出并删除最新的一次保存
git stash (save | push) 等同于 git stash
```

提升用法
```
git stash apply 从所有保存的中推出最新的一次保存，但并不删除这次保存
git stash (pop | apply) (1 | stash@{1}) 从所有保存的中推出次新的一次保存；1表示次新的顺序，可以是任何存在的顺序
git stash save --keep-index 只保存未被添加到git暂存区（Stage）的修改
git stash save 'message' 保存时，自定义保存信息
git stash push -m 'message' -- <pathspec> 只保存特定路径的文件，并自定义保存信息
```

### branch
```
git branch -r 查看远程分支
git branch -a 查看所有分支
git branch -D localBranch 删除本地分支
git branch -vv 查看分支绑定的远程主机地址和远程分支
git branch --track/--set-upstream-to localBranch origin/remoteBranch
git branch -m <老的分支名> <新的分支名>
```

### diff
```
git diff <file name>/<branch name> 对比文件、分支
```

### merge、rebase
```
git merge/rebase 远程主机名/分支名（origin/master） 在当前分支上合并"origin/master"
```

### push
```
git push 远程主机名 分支名
git push --set-upstream origin 分支名 (To push the current branch and set the remote as upstream)
```
如果要push到gerrit上面用：
```
git push 远程主机名 本地分支名: refs/for/远程分支名
```

### reset
回退到之前一个版本:
```
git reset --hard HEAD~1
```

### blame
查看文件的每一行是哪个提交最后改动的:
```
git blame filename
```

### log 
查看文件的详细提交记录，具体修改过哪些地方:
```
git log -p filename
```

### 修改默认编辑器

在GIT配置中设置 core.editor
```
git config --global core.editor "vim"
```

### 删除untracked files

`git clean -f`