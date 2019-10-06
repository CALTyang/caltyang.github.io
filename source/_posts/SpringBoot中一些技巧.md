---
title: SpringBoot中一些技巧
date: 2016-12-09 22:38:32
tags: Java
---

本文记录使用到的一些SpringBoot技巧
<!--more-->

## 重名Bean

可以考虑增加下面的配置来允许重名Bean
```
spring.main.allow-bean-definition-overriding=true
```

## 自定义配置
可以结合使用 @ConfigurationProperties(prefix="my”) 来配合 yml 文件

## Response中指定LocalDateTime的序列号格式
在model对应的字段上使用注解@JsonFormat，如下，[参考](http://lewandowski.io/2016/02/formatting-java-time-with-spring-boot-using-json/)
```java
@JsonFormat(pattern = “YYYY-MM-dd HH:mm:ss”)
private LocalDateTime canServiceBaseTime;
```


## 开启Gzip
配置项，[参考](https://www.cnblogs.com/larryzeal/p/7220291.html)
```
server.compression.enabled=true
server.compression.mime-types=application/json,application/xml,text/html,text/xml,text/plain
server.compression.min-response-size=1024
```

## 命令行中指定配置文件路径
使用类似命令
```bash
java -jar iop-analysis-1.0.0.jar --spring.config.location=./application.yml
```
使用 —spring.config.location 来指定配置文件路径 [参考](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## 配置全局项目路径 ContextPath
类似Tomcat中需要加上项目路径，可以在application.properties中增加配置
```
spring.application.name=springboot-demo
server.context-path=/${spring.application.name}
```

## Reference
[Spring Boot 属性配置和使用](http://blog.csdn.net/isea533/article/details/50281151)
    
