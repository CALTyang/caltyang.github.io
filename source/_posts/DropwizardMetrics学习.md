---
title: Dropwizard Metrics学习
date: 2019-04-23 22:50:09
tags: Java
---


[TOC]

Maven使用时添加依赖
```xml
<dependencies>
    <dependency>
        <groupId>io.dropwizard.metrics</groupId>
        <artifactId>metrics-core</artifactId>
        <version>${metrics.version}</version>
    </dependency>
</dependencies>
```

<!--more-->

## Metric种类
### Meters
### Gauge
用来衡量一个值的实时值，使用案例
```java
        metrics.register(MetricRegistry.name(QueueManager.class, name, "size"),
                         new Gauge<Integer>() {
                             @Override
                             public Integer getValue() {
                                 return queue.size();
                             }
                         });
```

### Counter
就是一个AtomicLong对象，可以增加或者减少

### Histograms
可以用来统计一个值的数据分布，其同时提供了下面几种统计指标
* 中间值
* 75%的值
* 90%的值
* 95%的值
* 98%的值
* 99%的值

### Timers
可以用来衡量一段代码的调用时间，示例
```java
private final Timer responses = metrics.timer(name(RequestHandler.class, "responses"));

public String handleRequest(Request request, Response response) {
    final Timer.Context context = responses.time();
    try {
        // etc;
        return "OK";
    } finally {
        context.stop();
    }
}
```



### HealthCheck
可以用来判活

## Reporter
### ConsoleReporter

### 通过JMX输出报告
需额外增加依赖
```xml
<dependency>
    <groupId>io.dropwizard.metrics</groupId>
    <artifactId>metrics-jmx</artifactId>
    <version>${metrics.version}</version>
</dependency>
```
通过类似VisualVM可以看到类似的结果图

![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190423/metric-jmx.png)

### 通过HTTP输出报告
需增加依赖
```xml
<dependency>
    <groupId>io.dropwizard.metrics</groupId>
    <artifactId>metrics-servlets</artifactId>
    <version>${metrics.version}</version>
</dependency>
```

## 核心类
### MetricRegistry
用来全局管理Metric，一般一个应用保留一个Registry即可
> 注意一个metric在一个MetricRegistry中名字要唯一



## 使用注解方式接入
参考 [metrics-aspectj](https://github.com/astefanutti/metrics-aspectj)

## Reference
* [官网](https://metrics.dropwizard.io/)
* [基于dropwizard/metrics ，kafka，zabbix构建应用统计数据收集展示系统](http://www.importnew.com/23001.html)
* [Metrics 是个什么鬼 之入门教程](http://wuchong.me/blog/2015/08/01/getting-started-with-metrics/)
* [Intro to Dropwizard Metrics](https://www.baeldung.com/dropwizard-metrics)
* [Dropwizard Metrics-core介绍和应用](http://www.heartthinkdo.com/?p=2494)
* [Restful Java Metering by Dropwizard Metrics](https://dzone.com/articles/restful-java-metering-by-dropwizard-metrics)
