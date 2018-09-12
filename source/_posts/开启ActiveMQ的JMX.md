---
title: 开启ActiveMQ的JMX
date: 2017-02-16 19:36:42
tags: Java
---

。以5.12.0为例
。之前版本略有不同，主要是 ACIVEMQ_SUNJMX_xxx 的地方不同，老版本可能直接放在了 activemq 的 shell 脚本中，5.12 移至和脚本同级目录的 env 下
<!--more-->

## 0x01 步骤
***
1）修改 conf/activemq.xml 中配置
。修改 broker 标签，添加属性 userJmx
![](/images/20160216/1.png)
2）修改 bin/env 配置
![](/images/20160216/2.png)

。取消59行左右配置，并增加：
```
ACTIVEMQ_SUNJMX_START="$ACTIVEMQ_SUNJMX_START -Djava.rmi.server.hostname=192.168.1.61"
```
。具体 IP 以实际为主
最后效果：
![](/images/20160216/3.png)

3）修改 conf/ 下面 jmx.access 的权限，为 600，直接 chmod 600 jmx.access 即可
![](/images/20160216/4.png)
。不然会报错

4）增加用户：
。这里涉及2个文件：jmx.access、jms.password
> access 文件用来配置角色（权限），比如读写是 readwrite，只读是 readonly
> password 是实际用户名

。这里增加一个读消息的账户，结果如下：
![](/images/20160216/jmxaccess.png)
![](/images/20160216/jmxpassword.png)

## 0x02 测试：
***
1）jmx 的连接地址为
```
service:jmx:rmi:///jndi/rmi://192.168.1.16:11099/jmxrmi
```
。ip 以实际为准

2）用户名和密码按照上述为 caltyang helloworld，使用 jconsole 测试连接
![](/images/20160216/7.png)
