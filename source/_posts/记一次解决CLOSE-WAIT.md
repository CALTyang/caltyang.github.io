---
title: 记一次解决CLOSE_WAIT
date: 2017-12-23 19:45:42
tags: 网络
---

。今天在工作中遇到了Nginx请求大量CLOSE_WAIT的状态，如图:
![](/images/20171223/problem.jpg)
<!--more-->
## 什么是CLOSE_WAIT
***
。状态流程：
![](/images/20171223/close_wait.jpg)
。我们知道，TCP有3次握手，4次挥手，CLOS_WAIT就是挥手过程中的一个状态
> 指的是客户端发送完挥手的 FIN 包之后，服务器端还没来得及返回 ACK 的状态

。其中MSL是Maximum Segment Lifetime，一般是60s，Linux下可以使用下面2个命令查看
```
   sysctl net.ipv4.tcp_fin_timeout
   cat /proc/sys/net/ipv4/tcp_fin_timeout
```
![](/images/20171223/ms.jpg)

## 具体原因
***
。参考：http://cxytiandi.com/blog/detail/11147

## 解决
***
。解决方式相对还是比较简单的，增加Nginx的配置即可（注意是在HTTP块中）
```
tcp_nodelay    on;
```

## Reference
***
[http://www.cnblogs.com/Bozh/p/3752476.html](http://www.cnblogs.com/Bozh/p/3752476.html)
[https://www.qcloud.com/community/article/164816001481011814](https://www.qcloud.com/community/article/164816001481011814)
[https://huoding.com/2016/01/19/488](https://huoding.com/2016/01/19/488)
