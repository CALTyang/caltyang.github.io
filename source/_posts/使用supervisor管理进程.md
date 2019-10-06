---
title: 使用supervisor管理进程
date: 2017-03-08 13:31:22
tags: Linux
---

## 简介
***
。Supervisor是一个基于Python的进程管理工具，可以用来监督某个进程是否持续运行，如果意外退出，自动启动等

## 安装配置
***
。安装可以直接使用pip进行安装
```bash
sudo pip install supervisor
```
。默认的配置在 /etc/supervisor 下
。Supervisor配置支持include，所以我们可以针对一个进程写一个配置文件，然后在总的配置文件中include这些文件（支持通配符）
<!--more-->

```
[include]
files = /etc/supervisor/*.ini
```
我们可以建立一个 /etc/supervisor 文件夹，用来存放对应进程的配置文件

。使用下面命令启动Supervisor
```
sudo supervisor
```
。这里可能出现提示，可以忽略


## 组成
***
。Supervisor有2部分组成
> supervisord: 后台进程
> supervisorctl: 命令行里控制入口

。因为是管理进程，所以最好supervisorctl添加权限认证，取消 /etc/supervisor.conf 下 username 和 password 的注释

```
[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
username=admin              ; should be same as http_username if set
password=admin                ; should be same as http_password if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available
```

。实际使用类似WebUI中，可以用来控制配置好的进程
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20170308/supervisorctl.png)

## 配置进程
***
。下面是一个配置Redis的例子
```
[program:redis]
command=/usr/local/bin/redis-server /usr/local/etc/redis.conf
autostart=true
autorestart=true
# stdout_logfile=/tmp/supervisor.log
```

。进程配置文件在 /etc/supervisor 下，是 ini 格式，具体可以参考官网，以 [program:xxxxx] 来标识一个进程
> command: 用于配置启动命令
> autostart: 是否自动启动
> autorestart: 是否自动重新启动

。Supervisor有一个扫描进程情况的周期，周期时间也在 /etc/supervisor 配置中

## WebUI
***
。Supervisor有一个內建的Web管理界面，可以通过取消 /etc/supervisor.conf 下面的配置设置
```
[inet_http_server]         ; inet (TCP) server disabled by default
port=127.0.0.1:9001        ; (ip_address:port specifier, *:port for all iface)
username=admin              ; (default is no username (open server))
password=admin               ; (default is no password (open server))
```

。通过浏览器访问 localhost:9001 可以看到界面
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20170308/webui.png)


## Reference
***
[Python 进程管理工具 Supervisor 使用教程](http://www.restran.net/2015/10/04/supervisord-tutorial/)
[使用 supervisor 管理进程](http://liyangliang.me/posts/2015/06/using-supervisor/)
