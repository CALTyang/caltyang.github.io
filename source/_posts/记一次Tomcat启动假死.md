---
title: 记一次Tomcat启动假死
date: 2019-07-02 22:40:55
tags: Java
---

现象：Tomcat启动假死，无报错
原因：可能是默认JDK使用的是系统的/dev/random来产生随机数，其在熵池不够时会一致等待，所以造成了假死
解决：可以编辑 $JAVA_HOME/lib/security/java.security 文件，修改如下面这样
```bash
securerandom.source=file:/dev/urandom
```

## Reference
* [Tomcat 7/8 出现假死现象，熵池阻塞变慢详解](http://www.zyizou.com/archives/384)
* [阿里云tomcat启动卡死解决方法](https://blog.csdn.net/vah101/article/details/78064249)
* [SecureRandom生成随机数超慢 导致tomcat启动时间过长的解决办法](https://blog.csdn.net/upshi/article/details/54907464)