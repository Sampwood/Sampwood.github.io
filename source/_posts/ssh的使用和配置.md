---
title: ssh的使用和配置
categories:
  - coding
tags:
  - environment
date: 2019-02-28 13:55:00
update: 2019-02-28 13:55:00
---


### 检查已经存在的ssh keys

```bash
ls -al ~/.ssh
```

### 新建ssh key

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

#### 使用ssh-agent来管理ssh key

```bash
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_rsa
```

<!-- more -->

### 修改ssh key的密码

```bash
ssh-keygen -f ~/.ssh/id_rsa -p
```

### 配置不同的host使用不同的ssh

在`~/.ssh`文件夹下，新建`config`文件并写入下面的代码
```
Host xxx.com
  HostName 192.168.1.1
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/sampwood_id_rsa
  IdentitiesOnly yes
Host gitlab.com
  HostName gitlab.com
  User git
  IdentityFile ~/.ssh/sampwood_id_rsa
  IdentitiesOnly yes


```

## 参考
1. [Connecting to GitHub with SSH](https://help.github.com/en/articles/connecting-to-github-with-ssh)
