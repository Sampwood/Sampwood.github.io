---
title: git基础操作
date: 2017-07-24 19:14:46
tags: git
---
记录一些git操作

### fetch

用于获取远程仓库代码:

```
git fetch <远程主机名> <分支名>
```

fetch下来的代码，在本地主机上可以用`远程主机名/分支名`的形式读取。

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



### branch
```
git branch -r 查看远程分支
git branch -a 查看所有分支
git branch -vv 查看分支绑定的远程主机地址和远程分支
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