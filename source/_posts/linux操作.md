---
title: linux操作
date: 2017-10-12 09:40:17
categories:
- coding
- linux
tags: linux
toc: true
---

### 用户命令
- 查看用户列表：cat /etc/passwd|grep -v nologin|grep -v halt|grep -v shutdown|awk -F":" '{ print $1"|"$3"|"$4 }'|more
- 查看组信息：cat /etc/group
- 查看用户所在组：id  user、groups user
- 修改密码：passwd (*user*)

<!--more-->
- 查看登录日志：sudo tail -n 10 /var/log/auth.log
- 修改用户名：(admin1 --> Sam)
> 1. 以root身份登录
> 2. usermod -l Sam admin1(改用户名)
> 3. usermod -c Sam Sam(改用户注解)
> 4. usermod -md /home/Sam Sam(改用户主目录)
- 修改组名：groupmod -n sam admin(admin ---> sam)
- 添加用户：useradd –d /home/sam -m sam
- 给用户添加组：usermod -a -G groupName user
- 删除用户：userdel *user*
- 删除组：groupdel *group*
- 查看所有组：cat /etc/group
- 查看所有用户： cat /etc/passwd、cat /etc/shadow


### 系统命令
- 查看内核版本：uname -a
- 查看操作系统版本：lsb_release -a、cat /proc/version
- 查看内存：grep MemTotal /proc/meminfo
- 查看系统有哪些shell: cat /etc/shells
- 登录用户行为：w
- 注册开机脚本：update-rc.d mongodb defaults
- 移除开机脚本：update-rc.d -f mongodb remove
- 增加环境变量：修改/etc/profile，在最下面增加：export PATH=$PATH:/usr/local/mongodb/bin
- 建立软链接指向环境path: sudo ln -s /opt/node-v6.11.2-linux-x64/bin/vue /usr/local/bin/vue
- 让/etc/profile文件修改立即生效：source /etc/profile

### 服务
- 注册服务：在/etc/init.d/目录下新建文件如：mongodb。
- 让服务文件修改生效：systemctl daemon-reload
- 查看服务申明的命令：service mongodb
- 查看服务状态：service mongodb status
- 查看进程：ps -ef
- 查看端口占用：netstat –apn

### shell命令
- 创建SSH Key：ssh-keygen -t rsa -C "youremail@example.com"
- 使命令脱离终端在后台运行：nohup 你的shell命令 &


### 文件命令
- 解压文件：
> 1. *.tar 用 tar -xvf 解压
> 2. *.gz 用 gzip -d或者gunzip 解压
> 3. *.tar.gz 和 *.tgz 用 tar -xzf 解压
> 4. *.bz2 用 bzip2 -d或者用bunzip2 解压
> 5. *.tar.bz2用tar -xjf 解压
> 6. *.Z 用 uncompress 解压
> 7. *.tar.Z 用tar -xZf 解压
> 8. *.rar 用 unrar e解压
> 9. *.zip 用 unzip 解压
> 10. *.tar.xz 先用 xz -d 解压成tar文件
- 移动/重命名文件：`mv A B`
- 修改文件权限：`chmod [option] MODE <file>`
- 修改文件拥有者：`chown [-R] 账号名称:用户组名称 文件或目录`
- 修改文件所属用户组：`chgrp [-R] 用户组名称 dirname/filename ...`
- 删除文件夹：`rm -rf <dir>`
- 删除文件：`rm -f <file>`
- 运行sh文件的几种方法：`sh test.sh`, `bash test.sh`, `./test.sh`, `source test.sh`

### 系统配置
- 修改系统时区：
> 1. rm -rf /etc/localtime
> 2. ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
- 修改系统时间：date -s "2008-08-08 12:00:00"