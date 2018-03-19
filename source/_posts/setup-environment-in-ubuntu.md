---
title: setup environment in ubuntu
categories:
  - coding
  - linux
tags:
  - environment
date: 2018-03-17 12:42:38
update: 2018-03-17 12:42:38
---

1. 添加用户：`useradd –d /home/sam -m sam`
2. 添加密码：`passwd sam`
3. 给用户添加组(添加sudo功能)：`usermod -aG sudo,adm sam`
4. 修改服务端使用的shell：`sudo vim /etc/passwd`，编辑/etc/passwd文件，给新建的用户指定shell解释器，添加**/bin/bash**：`sam:x:1000:1000::/home/sam:/bin/bash`
5. 更新apt源：`sudo apt-get update`
6. [安装git](https://git-scm.com/download/linux)：`# add-apt-repository ppa:git-core/ppa` `# apt update; apt install git`
7. [安装node](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)：`curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -` `sudo apt-get install -y nodejs`
