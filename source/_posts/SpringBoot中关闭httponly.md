---
title: SpringBoot中关闭httponly
date: 2016-09-01 14:49:26
tags: Java
---

。本文记录 SpringBoot 中关闭 httponly 的方法

## 0x01 什么是 httponly
***
。我们知道，Cookie 是服务器写在浏览器本地的一个文件，可以记录一些信息，比如用户登陆之后的信息之类
。Tomcat 服务器等，默认会写入一个名为JSESSIONID 的 Cookie
<!--more-->


。因为Session是存在于服务器端的，而HTTP请求由是无状态的，那么当一个请求到达服务器，我们怎么样才能区分这个请求来自哪个浏览器（用户）呢？

> 关键就是这个 SESSIONID，Session 实际就是服务器中的一块内存区域，通过将这块内存区和这个sessionId建立映射关系，我们可以实现对用户信息的确定

。所以，开发中经常遇到一些奇怪的问题，比如最近弄Shiro时，登陆认证的时灵时不灵，可能就是这个Cookie造成的
。而我们又知道，Cookie 的存在，有一定安全问题，即 CORS 之类的，具体不做介绍。所以出现了一个简单的方式，即httponly ，来规定 js 能否访问 Cookie
。默认我们使用如下代码，是可以看到浏览器中当前域存的一些 Cookie 的
```
document.cookie
```
。但是观察 Chrome 可以发现，并不是所以的信息都能取到（标识了httponly 的无法获取）


## 0x02 SpringBoot 中关闭 httponly
***
。SpringBoot 中默认是开启了HttpOnly 的，即上面讲的JSESSIONID 开启了，如上图：
。关闭这个特性也比较简单，使用下面的代码即可
```java
@Bean
public EmbeddedServletContainerFactory servletContainer() {
    TomcatEmbeddedServletContainerFactory factory = new TomcatEmbeddedServletContainerFactory();
    factory.setTomcatContextCustomizers(Arrays.asList(new CustomCustomizer()));
    return factory;
}
static class CustomCustomizer implements TomcatContextCustomizer {
    @Override
    public void customize(Context context) {
        context.setUseHttpOnly(false);
    }
}
```

。测试
。当然，如果你是通过类似建立一个 WebConfig 的 java 类来实现配置，注意不要忘记加上@Configuration 注解

## Reference
***
[StackOverFlow-Turn off HttpOnly Spring boot](https://stackoverflow.com/questions/22428233/turn-off-httponly-spring-boot)