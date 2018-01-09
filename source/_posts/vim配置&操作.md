---
title: vim配置&操作
categories:
  - coding
  - linux
tags:
  - linux
  - vim
date: 2018-01-08 16:08:05
update: 2018-01-08 16:08:05
---

### 单个命令：
  x: 删除当前光标所在的一个字符
  p: 粘贴
  hjkl: 移动光标（左下上右）
  d: 删除 （dd就是删除一行）
  a: 在光标后插入
<!-- more -->
  o: 在当前行后插入一个新行
  O: 在当前行前插入一个新行
  0: 到行头
  ^: 到本行第一个不是空格字符的位置
  $: 到本行行尾
  /pattern: 搜索pattern的字符串（按n键到下一个匹配的字符串）
  u: 后退修改
  ctrl+r: 前进修改
  G: 到最后一行
  w: 到下一个单词的开头
  e: 到下一个单词的结尾
  %: 匹配当前括号对应的另一半（首先得把光标移动到括号上）
  *: 匹配光标所在的单词的下一个
  #: 匹配光标所在的单词的上一个
  y: 拷贝 （yy就是拷贝一行）
  f: （fa）查找下一个字符（a）所在位置 （F查找上一个）
  t: （t,）查找下一个字符（,）前面位置 （T查找上一个）
  v: 可视化模式 （va包含要范围---闭区间，vi不包含范围---开区间）
  ctrl+v: 块操作
  ctrl+n/ctrl+p: 在insert模式下，自动补齐

### 组合：
  cw: 替换从光标所在位置到一个单词结尾的字符
  gg: 到第一行
  g_: 到本行最后一个不是空格的位置
  gU: 变大写
  gu: 变小写

### vim vundle
  vundle是vim的插件管理工具，它能够搜索、安装、更新和移除vim插件，再也不需要手动管理vim插件。

#### 安装
  1. git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
  2. 配置Vundle：修改`~/.vimrc`文件
```
set nocompatible              " 去除VI一致性,必须
filetype off                  " 必须

" 设置包括vundle和初始化相关的runtime path
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" 另一种选择, 指定一个vundle安装插件的路径
"call vundle#begin('~/some/path/here')

" 让vundle管理插件版本,必须
Plugin 'VundleVim/Vundle.vim'

" 以下范例用来支持不同格式的插件安装.
" 请将安装插件的命令放在vundle#begin和vundle#end之间.
" Github上的插件
" 格式为 Plugin '用户名/插件仓库名'
Plugin 'tpope/vim-fugitive'
" 来自 http://vim-scripts.org/vim/scripts.html 的插件
" Plugin '插件名称' 实际上是 Plugin 'vim-scripts/插件仓库名' 只是此处的用户名可以省略
Plugin 'L9'
" 由Git支持但不再github上的插件仓库 Plugin 'git clone 后面的地址'
Plugin 'git://git.wincent.com/command-t.git'
" 本地的Git仓库(例如自己的插件) Plugin 'file:///+本地插件仓库绝对路径'
Plugin 'file:///home/gmarik/path/to/plugin'
" 插件在仓库的子目录中.
" 正确指定路径用以设置runtimepath. 以下范例插件在sparkup/vim目录下
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" 安装L9，如果已经安装过这个插件，可利用以下格式避免命名冲突
Plugin 'ascenator/L9', {'name': 'newL9'}

" 你的所有插件需要在下面这行之前
call vundle#end()            " 必须
filetype plugin indent on    " 必须 加载vim自带和插件相应的语法和文件类型相关脚本
" 忽视插件改变缩进,可以使用以下替代:
"filetype plugin on
"
" 简要帮助文档
" :PluginList       - 列出所有已配置的插件
" :PluginInstall    - 安装插件,追加 `!` 用以更新或使用 :PluginUpdate
" :PluginSearch foo - 搜索 foo ; 追加 `!` 清除本地缓存
" :PluginClean      - 清除未使用插件,需要确认; 追加 `!` 自动批准移除未使用插件
"
" 查阅 :h vundle 获取更多细节和wiki以及FAQ
" 将你自己对非插件片段放在这行之后
```

  3. 插件安装：
    3.1 首先知道所需插件，vundle提供了`PluginSearch`命令来查找匹配的插件，eg. `PluginSearch foo`
    3.2 如果用`PluginSearch`命令找到了所需插件，则可以用`i`键来安装插件, eg.：
```
"Keymap: i - Install plugin; c - Cleanup; s - Search; R - Reload list
"Search results for: foo
Plugin 'MarkdownFootnotes'
Plugin 'VimFootnotes'
Plugin 'foo.vim'
```
      安装好之后还是要在`.vimrc`文件配置插件源。
    3.3 如果直接知道插件源在哪，可直接在`.vimrc`文件中配置（配置规则遵守第2步），然后用`PluginInstall`安装
    3.4 设置插件开关的键映射，在`.vimrc`文件的最后加入配置语句。例如NERDTree插件：
```
" NERDTree shortcut
map <C-n> :NERDTreeToggle<CR>
```
