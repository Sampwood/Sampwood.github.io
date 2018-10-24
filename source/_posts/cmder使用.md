---
title: cmder使用
categories:
  - coding
tags:
  - environment
date: 2018-10-24 20:45:03
update: 2018-10-24 20:45:03
---

cmder是一个超棒的windows命令窗口。

### 下载
下载地址： http://cmder.net/

一般都只用下载mini版本就可以了，因为git都会自己下载 = =

### 配置

因为下载的mini版本，所以要使用bash的话就得自己配置git bash的路径

打开设置(win+alt+p)，在`StartUp`下面的`Tasks`中，点击右边`Predefined tasks`列表新建一个task
或者直接修改其中的`{bash::bash}`，在右侧最下面的文本框中填入git bash的路径。

例如： `cmd /c ""C:\Program Files\Git\bin\bash" --login -i"`

同时，修改`{bash::bash as Admin}`: `*cmd /c ""C:\Program Files\Git\bin\bash" --login -i"`

有此可见，`*`表示是否由管理员运行

**在`StartUp`中可以设置默认启动的task：在`Specified named task`选择想要的task**

当然，配置好之后记得点击保存