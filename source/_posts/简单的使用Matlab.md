---
title: 简单的使用Matlab
date: 2015-08-15 14:07:07
tags: 工具
---

作为一名工科生，平时专业上用的最多的可能就是Matlab了，但是其巨大的资源消耗，以及笨重的GUI界面总是让人很不爽，很久以前看到了直接在命令行中进行matlab操作的方法，实践了一下感觉不错，做个记录。

主要分为2个平台：Linux、OS X …. (Windows自动屏蔽）：

## 0x01 Linux：
***
Linux 相对简单，安装过Matlab后，直接在bashrc中配置一个alias即可
```
export JAVA_HOME="/opt/jdk"
export PATH=$PATH:JAVA_HOME/bin
alias matlab="matlab -nosplash -nodesktop"
```
<!--more-->

如图，我使用的是zsh，直接在~/.zshrc中添加相应配置，需要附带两个参数 -nosplash -nodesktop ，是用来跳过图形界面和开启的启动界面的，当然 matlab 需要已经加入 $PATH 中。下面是更详细的启动参数：
。效果图：
![](/images/20150810/effect1.png)


## 0x02 OS X：
***
类似Linux（毕竟人家也是 Unix-like 么），zshrc 配置如下：
```
export MATLAB_HOME="/Applications/MATLAB_R2014b.app"
export PATH=$PATH:$MATLAB_HOME/bin
alias matlab="matlab -nodesktop -nosplash"
```

。效果图：
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20150810/effect2.png)

## 0x03 总结
***
经测试，不用原生的界面在性能上感觉好了不少，相对电量的消耗也有改善，而且直接操作终端更带感，当然，操作本身没有什么技术含量，很多跨平台的工具应该都能用类似的方案解决，不过是参数怎么给而已。
