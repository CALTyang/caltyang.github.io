---
title: 记OSX中遇到的一次Finder文件灰化的问题
date: 2018-01-14 15:18:08
tags: OSX
---

。这几天沉迷于看《晓说》，加上家里的NAS（群晖）也到位了，就尝试了一下把晓说的视频扒下来，留作收藏，结果在整理文件的时候遇到了一个挺有趣的事情，所以记录一下：
<!--more-->
> 如下图，Finder中某些文件莫名的灰化了，且也不能修改文件名

![](/images/20180114/nasfilelist.png)
原因：
> 不详

。解决方法：
1）使用命令查看文件
```bash
ls -@eln <file_path>
```
![](/images/20180114/lsresult.png)

2）复制显示结果中的com.apple.FinderInfo

3）使用命令（也可以配合使用通配符来批量操作）
```bash
xattr -d -r com.apple.FinderInfo <file_path>
```
。Done

# Reference
---
[os-x-folder-grayed-out](http://www.readern.com/os-x-folder-grayed-out.html)
[解决Macbook OSX文件/夹灰色不可复制](http://blog.csdn.net/evandeng2009/article/details/53242196)