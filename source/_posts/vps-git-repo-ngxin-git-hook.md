---
title: 在vps上搭建一个git仓库并实现代码提交之后自动部署到nginx上
categories:
  - coding
  - linux
tags:
  - git
  - linux
date: 2018-10-17 16:27:19
update: 2018-10-17 16:27:19
---


现在一般自己玩的项目都会托管在github上，然后使用github提供的git仓库地址来维护自己的代码。
例如`git@ github.com:Sampwood/sampwood.github.io.git`。

某一天，忽然想要开发一个私人的项目玩，感觉放在github上就不太好， 毕竟一些私人的东西不好放上去。
于是决定在自己的vps上搭建一个git仓库来维护自己的代码。

<!-- more -->

### vps上搭建git仓库

对比github代码仓库ssh地址`git@ github.com:Sampwood/sampwood.github.io.git`
和ssh登陆vps的命令`ssh root@**.**.**.**`。
我们可以这么理解代码仓库使用的ssh地址，就是git用户下Sampwood文件夹下的sampwood.github.io.git文件夹下的git仓库。

所以我们需要在vps上的操作就是安装git，创建git用户，以及新建git项目。

#### 安装git

对于Ubuntu，只要两步：

`$ add-apt-repository ppa:git-core/ppa`, `$ apt update; apt install git`

具体其他的可参考官网：[Download for Linux and Unix](https://git-scm.com/download/linux)

#### 创建git用户

新建git用户并添加到sudo组： `$ adduser git --ingroup sudo`

##### 使用ssh密钥

使用git bash来生成ssh key：
`$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

*PS: 为避免每次输入ssh key的密码，可以使用`ssh-agent`来管理ssh key。*

然后上传key到vps：
`ssh-copy-id git@**.**.**.**`

#### 新建git项目

作为git仓库，一般只建一个bare仓库，因为不会直接在这里进行代码修改。
bare仓库不会包含实际项目源文件的拷贝，只有.git目录下的文件
```sh
$ cd ~ 
$ mkdir blog.git && cd blog.git
$ git init --bare
```

到这里，我们在vps上搭建git仓库的操作已经结束。
我们可以使用`git clone git@**.**.**.**:blog.git`来克隆代码了。


### git hook响应push自动部署代码

blog.git是速度比较快的bare仓库，并不是可直接使用的目录，
所以需要制作一个POST Hooks将这个仓库clone到临时文件夹，再拷贝到实际www目录。

对应`git push`的hook钩子是`post-receive`

```sh
$ cd ~/blog.git/hooks
$ touch post-receive
$ vi post-receive
```

使用下面的脚本：
```bash
#!/bin/bash -l

# define path
GIT_REPO=/home/git/blog.git
TMP_GIT_CLONE=/tmp/blog
PUBLIC_WWW=/var/www/blog

# clean tmp directory
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE

# do some work
cd $TMP_GIT_CLONE
npm i
npm run build

# clean public directory
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/dist/* ${PUBLIC_WWW}

# show done info
echo "githook: $(date): Done"
```

更改脚本权限和/var/www/blog权限
```sh
$ chmod +x post-receive
$ sudo chmod 775 -R /var/www/blog
```

**注意**：这个脚本是针对前端项目的，而且其实更好的做法是通知持续集成（continous integration）的服务器。

另外这里贴一个更好的`post-receive`脚本的写法： 
[How-to setup a simple git push deployment](https://gist.github.com/thomasfr/9691385)

**注意**：如果碰到`Permission denied`，请修改文件拥有者： `sudo chown git file`


### vps上搭建nginx

#### 安装nginx

Ubuntu默认的源中就有Nginx，所以安装是比较简单的。
首先，更新apt源，以便软件是最新的，然后就可以安装nginx:
```sh
sudo apt-get update
sudo apt-get install nginx
```

#### 配置防火墙

开始测试Nginx前，我们需要配置防火墙，以便允许外界访问nginx服务。Nginx在安装的时候使用ufw注册自己作为一个服务，这样对nginx的访问就会变得很容易。

显示所有ufw应用的配置：`sudo ufw app list`

我们可以得到一个配置的输出列表：
```
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```

我们可以看到，有三个Nginx的配置：

- **Nginx Full**: 这个配置打开 80端口和443端口
- **Nginx HTTP**: 这个配置只打开80 (普通, 未加密通信)
- **Nginx HTTPS**: 这个配置只打开 443 (TLS/SSL 加密通信 )

一般来说我们应该配置最严的限制，因为本文我们还没有配置SSL，所以我们只打开80端口。

我们执行：`sudo ufw allow 'Nginx HTTP'`

验证修改状态:

我们可以看到HTTP是被打开的：
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

#### 检查web server
安装完成后，Ubuntu 16.04 会自动启动 Nginx. 我们可以使用systemd 检查运行状态:
``systemctl status nginx``

输出:
```
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; disabled; vendor preset: enabled)
   Active: active (running) since Mon 2018-10-15 04:23:21 UTC; 2 days ago
     Docs: man:nginx(8)
  Process: 9064 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
  Process: 9061 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/ Main PID: 9068 (nginx)
    Tasks: 2
   Memory: 3.5M
      CPU: 150ms
   CGroup: /system.slice/nginx.service
           ├─ 9068 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─27117 nginx: worker process
```

服务已经正常启动，当然最好的确认方法是通过访问web页面的方式。

#### 管理 Nginx 进程
现在我们已经有nginx在运行了，我们可以再试一些管理命令：

- 停止nginx: `sudo systemctl stop nginx`
- 启动nginx: `sudo systemctl restart nginx`
- 修改配置文件后，平滑加载配置命令(不会断开用户访问）：`sudo systemctl reload nginx`
- 默认，nginx是随着系统启动的时候自动运行。如果你不想开机启动，那么你可以禁止nginx开机启动： `sudo systemctl disable nginx`
- 重新配置nginx开机自动启动: `sudo systemctl enable nginx`


#### Nginx的文件和目录

现在我们已经管理nginx了，接下来可以熟悉一下nginx的目录结构和一些重要的文件：

**网站文件位置**

- /var/www/html: 网站文件存放的地方, 默认只有我们上面看到nginx页面，可以通过改变nginx配置文件的方式来修改这个位置。

**服务器配置**

- /etc/nginx: nginx配置文件目录。所有的nginx配置文件都在这里。
- /etc/nginx/nginx.conf: Nginx的主配置文件. 可以修改他来改变nginx的全局配置。
- /etc/nginx/sites-available/: 这个目录存储每一个网站的"server blocks"。nginx通常不会使用这些配置，除非它们陪连接到  sites-enabled 目录 (see below)。一般所有的server block 配置都在这个目录中设置，然后软连接到别的目录 。
- /etc/nginx/sites-enabled/: 这个目录存储生效的 "server blocks" 配置. 通常,这个配置都是链接到 sites-available目录中的配置文件
- /etc/nginx/snippets: 这个目录主要可以包含在其它nginx配置文件中的配置片段。重复的配置都可以重构为配置片段。

**日志文件**

- /var/log/nginx/access.log: 每一个访问请求都会记录在这个文件中，除非你做了其它设置。
- /var/log/nginx/error.log: 任何Nginx的错误信息都会记录到这个文件中。

#### 修改nginx配置

直接修改`/etc/nginx/sites-available`下的`default`文件，
或者新建一个`blog`文件并注释掉`default`中的内容。

写入nginx配置：
```
server {
        listen 80;
        listen [::]:80;

        server_name blog.sampwood.top; // 根据域名转发到不同端口

        root /var/www/blog;
        index index.html;

        location / {
                root /var/www/blog;
                try_files $uri $uri/ @router;
                index index.html;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
                root html;
        }

        location @router {
                rewrite ^.*$ /index.html last;
        }
}
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        server_name _; // 默认nginx的访问页面

        root /var/www/html;

        index index.html index.htm index.nginx-debian.html;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

如果是新建了一个`blog`文件，那在`/etc/nginx/sites-enabled`的文件夹下新建一个软连接：
`ln  -s  /etc/nginx/sites-enabled/blog  /etc/nginx/sites-available/blog`

然后就可以用`nginx -s reload`重新启动`nginx`了。


> 到这里，差不多我们的工作就做完了。
> 现在，每次我们push代码之后，服务器会自动部署代码，我们也能访问nginx服务器看到新的改动。


## 参考
1. [使用Git在VPS上部署代码](https://noahgao.net/2015/git-deploy-vps/)
2. [Download for Linux and Unix](https://git-scm.com/download/linux)
3. [Connecting to GitHub with SSH](https://help.github.com/articles/connecting-to-github-with-ssh/)
4. [自定义 Git - Git 钩子](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)
5. [How-to setup a simple git push deployment](https://gist.github.com/thomasfr/9691385)
6. [如何在ubuntu 16.04 上安装Nginx](http://www.nginx.cn/4723.html)
7. [nginx 基本入门(至今为止见过最好的 nginx 入门文章，没有之一。)](https://www.jianshu.com/p/93ac21161ac6)
