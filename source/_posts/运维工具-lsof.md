---
title: 运维工具-lsof
date: 2014-09-23 22:27:36
tags: Linux
---

是list open filee的缩写，可以用来查看程序占用的文件句柄、socket连接等等，可以配合grep来显示指定进程的情况，默认显示的字段
```bash
COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME.
```
该命令可以显示的文件类型包括
1. 普通文件
2. 目录
3. 网络文件系统的文件
4. 字符或设备文件
5. (函数)共享库
6. 管道，命名管道
7. 符号链接
8. 网络文件（例如：NFS file、网络socket，unix域名socket）
9. 还有其它类型的文件，等等

<!--more-->
其支持的命令行参数：

| 参数名 | 含义 |
|---|---|
| -a | 列出打开文件存在的进程 |
| -c<进程名> | 列出指定进程所打开的文件 |
| -g | 列出GID号进程详情 |
| -d<文件号> | 列出占用该文件号的进程 |
| +d<目录> | 列出目录下被打开的文件 |
| +D<目录> | 递归列出目录下被打开的文件 |
| -n<目录> | 列出使用NFS的文件 |
| -i<条件> | 列出符合条件的进程。（4、6、协议、:端口、 @ip ）|
| -p<进程号> | 列出指定进程号所打开的文件 |
| -u | 列出UID号进程详情 |
| -h | 显示帮助信息 |
| -v | 显示版本信息 |

## 字段解释
### 字段含义

| 名称 | 作用 |
|---|---|
| COMMAND | 程序命令,默认以9个字符长度显示的命令名称。可使用+c参数指定显示的宽度，若+c后跟的参数为零，则显示命令的全名 |
| PID | 进程id |
| PPID | 父进程的IP号，默认不显示，当使用-R参数可打开 |
| PGID | 进程组的ID编号，默认也不会显示，当使用-g参数时可打开 |
| USER | 命令的执行UID或系统中登陆的用户名称。默认显示为用户名，当使用-l参数时，可显示UID |
| FD | 文件描述符 |
| TYPE | IPv4的包,IPv6包，DIR 目录，LINK 链接文件等 |
| DEVICE | 使用character special、block special表示的设备号 |
| SIZE/OFF | 文件的大小，如果不能用大小表示的，会留空。使用-s参数控制 |
| NODE | 本地文件的node码，或者协议，如TCP等 |
| NAME | 挂载点和文件的全路径（链接会被解析为实际路径），或者连接双方的地址和端口、状态等 |

### FP文件描述符类型

| 缩写 | 含义 |
|---|---|
| cwd | 表示current work dirctory，即：应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改 |
| txt | 该类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序 |
| lnn | library references (AIX) |
| er | FD information error (see NAME column) |
| jld | jail directory (FreeBSD) |
| ltx | shared library text (code and data) |
| mxx | hex memory-mapped type number xx |
| m86 | DOS Merge mapped file |
| mem | memory-mapped file |
| mmap | memory-mapped device |
| pd | parent directory |
| rtd | root directory |
| tr | kernel trace file (OpenBSD) |
| v86 | VP/ix mapped file |
| 0 | 表示标准输出 |
| 1 | 表示标准输入 |
| 2 | 表示标准错误 |

一般在标准输出、标准错误、标准输入后还跟着文件状态模式：r、w、u等
> * u：表示该文件被打开并处于读取/写入模式
> * r：表示该文件被打开并处于只读模式
> * w：表示该文件被打开并处于写入模式
> * 空格：表示该文件的状态模式为unknow，且没有锁定
> * -：表示该文件的状态模式为unknow，且被锁定

同时在文件状态模式后面，还跟着相关的锁
> * N：for a Solaris NFS lock of unknown type;
r：for read lock on part of the file;
> * R：for a read lock on the entire file;
> * w：for a write lock on part of the file;（文件的部分写锁）
> * W：for a write lock on the entire file;（整个文件的写锁）
> * u：for a read and write lock of any length;
> * U：for a lock of unknown type;
> * x：for an SCO OpenServer Xenix lock on part      of the file;
> * X：for an SCO OpenServer Xenix lock on the      entire file;
> * space：if there is no lock.

### TYPE类型
> 不同操作系统可能不一致

| 缩写 | 含义 |
|---|---|
| DIR | 表示目录 |
| REG | REGular file, file that show up in directory |
| CHR | 表示字符类型 |
| BLK | 块设备类型 |
| UNIX | UNIX 域套接字 |
| FIFO | 先进先出 (FIFO) 队列 |
| IPv4 | 网际协议 (IP) 套接字 |
| DEVICE | 指定磁盘的名称 |
| SIZE | 文件的大小 |
| NODE | 索引节点（文件在磁盘上的标识）|
| NAME | 打开文件的确切名称 |

## 常用场景
### 查询指定端口的程序情况
使用-i指定端口，如
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20140923/lsof-i.png)

### 查询指定pid的程序情况
使用-p来指定pid
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20140923/lsof-p.png)

### 查看指定文件夹的占用情况
使用+D指定文件夹
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20140923/lsof+D.png)

如果后面跟上了(deleted)，可能是存在文件句柄泄露问题i，参考笔记：[[问题]删除文件时 Device or Resource busy](https://app.yinxiang.com/shard/s15/nl/2367831/9e856844-3bfa-4c22-8721-e3d66c0ee8c9/)
临时解决对应问题可以考虑将Linux中ulimit调大，参考[Why is number of open files limited in Linux?](http://unix.stackexchange.com/questions/36841/why-is-number-of-open-files-limited-in-linux)
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20140923/lsof-sof.png)

## Reference
* [lsof命令详解](http://github.tiankonguse.com/blog/2016/08/14/command-lsof.html)
* [Linux Find Out Which Process Is Listening Upon a Port](http://www.cyberciti.biz/faq/what-process-has-open-linux-port/)
* [每天一个linux命令（51）：lsof命令](https://www.cnblogs.com/peida/archive/2013/02/26/2932972.html)
* [lsof命令](http://man.linuxde.net/lsof)
* [使用 lsof 查找打开的文件](https://www.ibm.com/developerworks/cn/aix/library/au-lsof.html)


