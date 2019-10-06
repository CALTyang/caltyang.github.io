---
title: SecureShell
date: 2013-06-22 13:13:10
tags: Linux
---

。SSH用于远程管理，与个人电脑不同，服务器一般都是运行在IDC机房中，所以我们通常不会直接接触服务器硬件，而是通过各种远程管理方式对服务器进行控制
。常见远程管理工具：
1）RDP(remote desktop protocol)协议
> windows远程桌面管理

2）telnet
> CLI界面下的远程管理，几乎所有操作系统都有
> 内容明文传输

3）SSH（secure shell）
> CLI界面下的远程管理
> 几乎所有OS都有（内容加密）
> 类UNIX系统下主要的远程管理方式（Linux，BSD，MacOS）

4）RFB（remote frame buffer）
> 图形化远程管理协议
> VNC（virtual network computing）使用的协议
> 主要作为类UNIX系统下主要的图形化远程管理方式
<!--more-->

## SSH
***
。是Linux、Unix、Mac及其他网络设备最常用的远程CLI管理协议
。其使用密钥对数据进行加密传输，保证了远程管理数据的安全性
。广泛使用的是 SSH2 版本，走的TCP协议，端口号 22
。openssh是ssh的一个开源程序，大多数Linux版本均使用openssh作为SSH程序，可以使用 apt-get(Debian等) 来安装
```
sudo apt-get install openssh-server
```
。为 C/S 结构，服务端默认启动，作为常驻服务运行
```
service sshd status
```
。命令ssh用以以ssh协议登录其他主机：
```
ssh root@192.168.1.1
ssh root@192.168.1.1 +命令
```
。第一次在两台主机之间建立SSH连接时，需要交换公钥用以进行加密
。ssh信息保存在用户家目录的.ssh隐藏文件夹下

## 配置免密码登陆
***
1）使用命令生成密钥
```
ssh-keygen
```
2）将公钥复制到目标主机的 ~/.ssh 目录下，并命名
```
cp id_rsa.pub authorized_keys
```
。复制到主机
```
scp ./authorized_keys pi@192.168.1.107:~/.ssh
```
。authorized_keys 是个文本文件，如果已存在，直接将内容 append 在文件末尾即可
。参考：http://www.cnblogs.com/jdksummer/articles/2521550.html

## 问题
***
。配置 ssh 免密码登录之后可能并不管用，还是需要输入密码
。可能是 authorized_keys 的权限问题，得配置为 600
```
chmod 600 ~/.ssh/authorized_keys
```

## 其他使用SSH的命令
***
1）scp
。命令scp用以用过ssh在两台计算机间进行快速的、加密的数据传输：
scp 源文件 目标地址
```
scp linuxcast.tar root@192.168.1.1:/root/(绝对路径)
```
。其他参数：
```
-r     递归的进行拷贝（用以传输文件夹）
-p     传输时保留文件权限及时间
-c     传输时进行数据压缩
```
。CyberDuck：一个mac上的文件传输工具，支持多种协议：

2）rsync
。用以在两台计算机之间通过ssh协议同步文件
```
rsync *.* root@192.168.1.1:/root/
```
rsync命令是一种增量同步方式，只会拷贝变化的文件（修改的、新增的、删除的）

## Reference
***
http://blog.csdn.net/kiki113/article/details/3405274
