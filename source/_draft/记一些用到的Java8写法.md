---
title: 记一些用到的Java8写法
date: 2018-09-12 15:24:13
tags:
---
本文记录使用Java8开发过程中的一些常用Gist

## 0x01 Optional
---
Optional可以用来很好的解决NPE的问题，尤其是在链式调用的时候，链式过程中，如果那个环节出现了Null，就会报NPE，令人头疼不止，其实通过Optional，我们可以在不修改原有代码逻辑（尤其是不修改Model等类中属性的代码，如Car car -> Optional<Car> car）来更好的进行链式调用
场景如下
> Person类，有个Car属性，Car属性有个Insurance属性（表示汽车归属的保险公司），Insurance有个name属性（字符串）
现在要有个person实例，要获取他的车的保险公司的名字，最简单的写法
```java
String insuranceName = person.getCar().getInsurance().getName()
```
<!--more-->
然而，如果person不为空，person有car，car买了保险，保险公司有名字，上面的代码是没有什么问题的，但是现实往往不是这样，如果person为空，person没有车，车没有买保险，保险公司没有名字，则就NPE了
。利用Optional改写，如下
```java
String insuranceName = Optional.ofNullable(person)
                               .map(Car::getInsurance)
                               .map(Insurance::getName)
                               .orElse("Unknown");
```
。注，上面是没有更改过Model层类声明的方式，如果你Model中属性已经包装上了Optional，那么 flatMap 了解一下~ 

## 0x02 新的时间API
---
。构造LDT
。LDT转LD
。LT
。计算LDT之间时间差
。获取Unix时间戳
。增删时区偏差

## 0x03 stream快速遍历转换集合
---



## Reference
---
