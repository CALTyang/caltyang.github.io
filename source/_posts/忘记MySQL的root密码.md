---
title: 忘记MySQL的root密码
date: 2014-12-09 14:01:20
tags: 数据库
---

。首先MySQL密码忘了，只能重置，不能找回
<!--more-->

## 5.7.x以前
***
1）kill 相关进程
2）使用 —skip-grant-tables 重启MySQL服务（跳过权限表认证）
```
./mysqld_safe —skip-grant-tables —user=root &
```

3）使用空密码root用户链接MySQL，修改密码
```
update user set password = password(‘123456’) where user = ‘root’;
```

。如果报错的话，注意切换数据库，换语句！

4）刷新权限表
```
mysql> flush privileges;
```

5）重新用root登陆

## 5.7 以后
***
![](/images/20141209/mysql5.7.png)

。由于官方移除了mysql数据库下user表的password字段，当然只是改了一个字段名：
。所以上面的 update 语句无法生效，换成下面的语句
```
update user set authentication_string = password(‘123456’) where user = ‘root’;
```

。记得 flush privileges

## Reference
***
。[http://jingyan.baidu.com/article/0320e2c198ad5f1b87507bc8.html](http://jingyan.baidu.com/article/0320e2c198ad5f1b87507bc8.html)
