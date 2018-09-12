---
title: SpringBoot中开启热部署
date: 2017-03-05 13:57:36
tags: Spring
---

SpringBoot中开启热部署
使用 spring-boot-devtool 可以用来实现热部署，即代码（比如接口）有变动，不需要重启应用，在保存代码时即会自动进行部署操作
。好像是在 SpringBoot 1.3+ 引入
![](/images/20170305/banner.png)
<!--more-->

使用步骤
1）引入依赖：
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

。注意一定要配置 optional 为 true，否则不生效
。同时需要开启 maven 的插件配置
```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <fork>true</fork>
    </configuration>
</plugin>
```

2）开启 IDEA 中自动编译的特性： 设置 → Build,Execution,Deployment → Compiler

3）运行项目
。自己测的时候，直接用 IDEA 的运行并没有成功，即修改代码之后没有触发重新部署
。使用 Maven 的 target 成功，即命令

```
mvn spring-boot:run
```


