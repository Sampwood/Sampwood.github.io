---
title: Linux文件系统层次结构（FHS）
date: 2017-10-12 09:41:17
categories:
- coding
- linux
tags: linux
---

文件可以用两种方式来区分：可否共享，以及是是否常常修改。把不同类别的文件放在不同的目录下，例如：

\ | 可共享 | 不可共享
---|--- | ---
静态 | /usr、/opt | /etc、/boot
时常修改 | /var/mail、/var/spool/news | /var/run、/var/lock
<!--more-->
## 主要文件目录


目录 | 作用 
---|--- 
/ | 根目录
/bin | 基础的二进制命令文件
/boot | 系统引导文件
/dev | 硬件设备文件
/etc | 配置文件，例如可以把数据库启动配置文件放在这
/etc/opt | 对应安装在/opt目录下的软件的配置文件，要求目录结构和/opt下一致
/home | 用户目录（/home/user）
/lib<qual> | 依赖文件
/media | 移动设备接入文件夹
/mnt | 临时挂载点
/opt | 用于添加应用程序（类似windows下的D:\software）
/root | 超级管理员目录
/sbin | 管理员的二进制命令文件
/srv | 系统服务所需数据存放
/tmp | 临时文件存放
/usr | 类似windows下的C:\文件夹，放全局目录
/usr/bin | 大多数的用户命令
/usr/lib<qual> | 程序的依赖文件
/usr/sbin | 非标准系统二进制文件
/usr/local | 类似windows下的C:\program files文件夹，本地目录，例如可以把数据库程序放在这
/usr/local/bin | 
/usr/local/lib<qual> | 
/usr/local/sbin | 
/var | 可变动数据存储
/var/lib | 变量状态信息，例如可以把数据库数据存放在这个目录下
/var/log | 日志文件，例如可以把数据库日志文件放在这