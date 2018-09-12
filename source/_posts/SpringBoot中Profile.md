---
title: SpringBoot中Profile
date: 2017-12-15 13:39:55
tags: Spring
---

。本文记录SpringBoot中多Profile的学习笔记

## 是什么
***
。在开发中，我们经常有针对不同的场景有不同的配置文件的需求，SpringBoot Profile就是一种解决该需求的方案
。通过针对不同场景的配置文件(profile)，我们可以配置代码到不同的使用场景上

<!--more-->

## 使用
***
。首先我们从YAML的方式开始，假设我们需求中有3种场景:
> local: 本地开发环境
> test: 测试环境，内网机器，用于测试
> prod: 生成环境，实际线上机器

。比如配置datasource，我们可以像下面这样配置
```
---
#             local环境
#----------------------------------------
spring:
  profiles: local
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/project_local?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: 123456
---
#             test环境
#----------------------------------------
spring:
  profiles: test
  datasource:
    url: jdbc:mysql://192.168.1.250:3306/project_test?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: 123456
---
#             prod环境
#----------------------------------------
spring:
  profiles: prod
  datasource:
    url: jdbc:mysql://122.118.1.25:3306/project_prod?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: 123456
```
。同时，我们可以指定一个默认开启的 profile，通过下面配置

```
spring:
  profiles:
    active: local

```

。这样，通过修改application.yml中对应active的profile，我们可以实现切换配置场景的需求

## 获取 Profile 中值
***
。获取 profile （YAML或property）文件中配置的变量，我们有2种方式

通过 @Value 注解
通过 @ 注解

。同时，如果我们自定义了一个Configuration类，我们可以通过配置prefix的方式来简化@Value的字段配置，代码如下
```
asdf
```

## 编程切换
***
。除了可以通过配置文件指定active的profile，我们还可以通过在代码中配置环境
。比如我们跑单例的时候使用的是H2的数据库，以避免造成实际数据库数据的问题，因此我们可以通过配置一个test的profile，其中配置了H2的datasource
```
。然后可以在测试代码中使用注解来指定profile
```java

## Tip
***
1) 其他方式指定 profile
。实际使用中经常遇到这样一个问题，即本地开发完之后忘记修改profile，导致显示Jenkins打包部署时还是使用的开发时开启的profile（比如local），导致无法连接，造成问题
。比较简单的方式是通过命令行指定active的profile

。同时，不光是可以通过命令行传参配置active，还有其他方式，同时，各个方式之间存在一个优先级，优先级高的配置有效

2) 不同的配置文件指定环境

## Reference
***
[官网文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html)
[Spring Boot Profile使用](http://blog.javachen.com/2016/02/22/profile-usage-in-spring-boot.html)