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
4. 更新apt源：`sudo apt-get update`
5. [安装git](https://git-scm.com/download/linux)：`# add-apt-repository ppa:git-core/ppa` `# apt update; apt install git`
6. [安装node](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)：`curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -` `sudo apt-get install -y nodejs`
