---
title: 使用jEnv管理JDK版本
date: 2016-12-16 19:24:22
tags: Java
---

。ruby开发者一般都熟悉这样一个东西，即rvm（Ruby Version Management），可以用来管理多个ruby环境
。其实Java环境中同样有这样一个工具用来管理多个JDK环境，即 jEnv
。官网：http://www.jenv.be/
<!--more-->
## 安装
***
。这里以OSX系统为例，我们可以直接使用Homebrew进行安装
```
brew install jenv
```
## 使用
***
。我们可以使用add来添加多个JDK
```
jenv add /path/to/java/home
```
。查看当前用jEnv管理的JDK版本
```
jenv versions
```
。指定一个JDK版本，这里分2种，全局和local，后面的xxx指定特定的版本
```
jenv global xxx
jenv local xxx
```

## OSX中Tip
***
。在OSX中，可以使用下面命令查看路径
```
/usr/libexec/java_home -V
```

## 问题
***
。虽然自己并没有什么使用多个版本JDK的需求，但是知道并安利一下这个小工具还是不错的
。然而自己在实际使用中，遇到了一个小问题，即某些时候，会无法正确识别JAVA_HOME等变量，其实配置开启一个插件即可
```
jenv enable-plugin export
```

## Reference
***
[https://github.com/gcuisinier/jenv](https://github.com/gcuisinier/jenv)
[https://boxingp.github.io/blog/2015/01/25/manage-multiple-versions-of-java-on-os-x/](https://boxingp.github.io/blog/2015/01/25/manage-multiple-versions-of-java-on-os-x/)
[http://www.lrj.name/post-show/id/5565a9d739b793000000000cc2](http://www.lrj.name/post-show/id/5565a9d739b793000000000cc2)
[ImportNew - 在OS X管理多个Java版本](http://www.importnew.com/14866.html)
