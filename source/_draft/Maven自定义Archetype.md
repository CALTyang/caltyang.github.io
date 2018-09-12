---
title: Maven自定义Archetype
date: 2017-12-21 23:26:45
tags: Java
---

本文记录自定义Maven Archetype的过程
因为自己现在经常写一些SpringBoot的项目，而构建SpringBoot要么通过SpringBoot的命令行工具，又或者通过官网的start界面快速生成一个项目，总是不能完美的满足自己的需求。很多自己常用的Maven依赖，还是需要手工去修改pom.xml，故萌生了自己构建一个Archetype的想法
<!--more-->
## 自带的 Archetype
***
我们使用IDEA新建Maven项目时，可以看到类似下面的界面，其中就是Maven自带的一些Archetype
![](/images/20171221/builtin_archetypes.png)
。其中，我比较常用的就是 maven-archetype-quickstart，然而查看其项目骨架中的pom.xml，除了一个junit之外，其他少的可怜，且其版本还是很老的版本
。所以我们开始动手构建一个Archetype，至少需要包含如下依赖
- 新版junit
- apache commons lang
- lombok

## 开始自定义
***

## Reference
***
《Maven实战》
