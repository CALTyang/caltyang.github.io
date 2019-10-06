---
title: Linux运行级别
date: 2014-09-05 13:23:12
tags: Linux
---

Linux运行级别
。Linux运行级别是一个初始化状态，是系统用来决定用来操作哪些服务
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20140905/runlevel.png)
<!--more-->

。运行级别通过数字标识，大多数 Linux Server 默认 run level 是 3，而大多数桌面OS是 5
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20140905/config.png)

。默认run level在/etc/inittab文件中设置，通过 initdefault 行来定义（CentOS / Fedora / Redhat / RHEL / Debian Linux.）
。修改 id 来调整默认 run level
。然后重启。

## 相关命令
***
。打印当前 run level
```
who -r
```

。查看 当前 和 前一个 run level
```
runlevel
```

。切换运行级别
```
sudo init 1
```

## Reference
***
http://www.tldp.org/LDP/sag/html/run-levels-intro.html
http://www.cyberciti.biz/tips/linux-changing-run-levels.html
http://www.cyberciti.biz/howto/question/linux/unix-linux-find-out-runlevel.php