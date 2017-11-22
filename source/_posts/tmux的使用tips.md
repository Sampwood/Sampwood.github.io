---
title: tmux的使用tips
date: 2017-09-26 14:32:30
categories:
- Terminal
tags: tmux
toc: true
---

### xshell中配置

Xshell下：文件->属性->终端->VT模式->初始VT模式->去掉 Auto Wrap Mode (DECAWM) 勾选->退出SSH重新登录一次：
<!--more-->
![xshell-Tmux](/images/xshell_Tmux.png)


### 快捷键

所有的快捷键都是在激活了按键功能之后才有用的，`ctrl+b`是激活按键功能的快捷键

- 新建panel：`"`(下方)，`%`(右侧)
- 切换到下一个panel：`o`
- 移动光标以选择面板：`方向键`
- 关闭当前panel：`x`
- 新建窗口：`c`
- 切换至上一窗口：`p`
- 切换至下一窗口：`n`
- 关闭当前窗口：`&`