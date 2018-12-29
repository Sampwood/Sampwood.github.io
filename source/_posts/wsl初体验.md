---
title: wsl初体验
categories:
  - coding
  - linux
tags:
  - linux
  - environment
date: 2018-12-29 16:04:52
update: 2018-12-29 16:04:52
---


wsl, 就是Windows Subsystem for Linux, 简单的说 WSL 就是让开发者在 Windows 上无需虚拟机就可以搭建一套 Linux 开发环境。

使用 WSL 的好处是：

1. 最主要的一点，你可以快速的开启 Linux 环境，之前我的做法是开启虚拟器，占用资源多而且打开慢。
2. 通过 window商店 选择多种 Linux 发行版，目前支持 Ubuntu(16.04 LTS)、openSUSE Leap 42、 SUSE Linux Enterprise Server。
3. 使用 Linux 命令行工具处理一些问题，例如 sed, awk 等。
4. 使用 Linux 内置包管理器安装一些软件，例如 git redis 等，基本上一条命令就能安装好。

<!-- more -->

### 安装WSL

1. 控制面板 -> 程序 -> 启用或关闭 Windows 功能 -> 勾选 适用Linux的Windows子系统 （我是秋季创造者更新版本，我记得之前低版本可能是 beta 版本）

   ![\images\wsl](..\images\wsl.jpg)

2. 打开 window商店，选择合适的 Linux 发行版安装 (这里搜索Linux 根据下面第一个suggestion 进入)，这里我选择了 Ubuntu

3. 重启电脑。打开 Ubuntu ，第一次需要等待一会，设置账号密码

如此，wsl就安装好了，可以使用ubuntu的命令行啦~~

### 配置命令行

既然可以使用ubuntu的bash，我们就可以配置bash，来让我们的操作更舒适。

例如安装zsh，

```bash
sudo apt-get install zsh
```

为了让某些theme能在windows下正常显示，还要安装`Powerline`字体，例如我就安装了`DejaVu Sans Mono for Powerline` 字体。
安装字体之后，就可以在命令行窗口设置字体了，设置之后显示就正常了。


#### zsh配置文件

目前使用的主题是`amuse`，也自定义了一些命令:

- gcb: git_get_branch 查看当前分支名
- gpg: git_push_gerrit 把当前分支的内容推送到gerrit的相同分支上


```
# If you come from bash you might have to change your $PATH.
# export PATH=$HOME/bin:/usr/local/bin:$PATH

# Path to your oh-my-zsh installation.
  export ZSH="/home/sam/.oh-my-zsh"

# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/robbyrussell/oh-my-zsh/wiki/Themes
ZSH_THEME="amuse"

# Set list of themes to pick from when loading at random
# Setting this variable when ZSH_THEME=random will cause zsh to load
# a theme from this variable instead of looking in ~/.oh-my-zsh/themes/
# If set to an empty array, this variable will have no effect.
# ZSH_THEME_RANDOM_CANDIDATES=( "robbyrussell" "agnoster" )

# Uncomment the following line to use case-sensitive completion.
# CASE_SENSITIVE="true"

# Uncomment the following line to use hyphen-insensitive completion.
# Case-sensitive completion must be off. _ and - will be interchangeable.
# HYPHEN_INSENSITIVE="true"

# Uncomment the following line to disable bi-weekly auto-update checks.
# DISABLE_AUTO_UPDATE="true"

# Uncomment the following line to change how often to auto-update (in days).
# export UPDATE_ZSH_DAYS=13

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"

# Uncomment the following line to display red dots whilst waiting for completion.
# COMPLETION_WAITING_DOTS="true"

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.
# You can set one of the optional three formats:
# "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# or set a custom format using the strftime function format specifications,
# see 'man strftime' for details.
# HIST_STAMPS="mm/dd/yyyy"

# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder

# Which plugins would you like to load?
# Standard plugins can be found in ~/.oh-my-zsh/plugins/*
# Custom plugins may be added to ~/.oh-my-zsh/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
  git,
  zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh

# User configuration

# export MANPATH="/usr/local/man:$MANPATH"

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
 if [[ -n $SSH_CONNECTION ]]; then
   export EDITOR='vim'
 else
   export EDITOR='mvim'
 fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# ssh
# export SSH_KEY_PATH="~/.ssh/rsa_id"

# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"
alias cdcode="cd /mnt/d/code"

function git_get_branch {
  br=`git branch | grep "*"`
  echo ${br/* /}
}
alias gcb="git_get_branch"
function git_push_gerrit {
  GIT_CURRENT_BRANCH=`git_get_branch`
  echo Current branch is $GIT_CURRENT_BRANCH
  `git push origin HEAD:refs/for/$GIT_CURRENT_BRANCH`
}
alias gpg="git_push_gerrit"
```


## 参考

1. [三流程序员的自我修养](https://www.cnblogs.com/javanoob/p/wsl.html)
2. [调教你的WSL终端](https://www.jianshu.com/p/b147735ff3f2)
