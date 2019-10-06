---
title: Python中时间
date: 2019-07-17 22:18:23
tags: Python
---

[toc]

Python自带时间和日期相关的包，常用对象有：
* datetime
* date
* time X2
* timestamp
* timezone
* timedelta

![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190717/python-datetime.png)
<!--more-->
> 使用datetime居多

## datetime
常用操作
```python
from datetime import datetime, timedelta

# 构建datetime对象
d = datetime(2019, 1, 1, 0, 0)

# 时间加减
d1 = d - timedelta(hour=1)
```
### 格式化相关
代码如下：
```python
# 对象序列化到字符串
d.strftime('%Y-%m-%d %H:%M:%S')

# 字符串反序列化到对象
d = time.strptime('2019-05-20 13:14:00', fmt='%Y-%m-%d %H:%M:%S'))
```
常用格式化符号：
| 符号 | 含义 |
|---|---|
| %y | 两位数的年份表示（00-99）|
| %Y | 四位数的年份表示（000-9999）|
| %m | 月份（01-12）|
| %d | 月内中的一天（0-31）|
| %H | 24小时制小时数（0-23）|
| %I | 12小时制小时数（01-12）|
| %M | 分钟数（00=59）|
| %S | 秒（00-59）|
| %a | 本地简化星期名称|
| %A | 本地完整星期名称|
| %b | 本地简化的月份名称|
| %B | 本地完整的月份名称|
| %c | 本地相应的日期表示和时间表示|
| %j | 年内的一天（001-366）|
| %p | 本地A.M.或P.M.的等价符|
| %U | 一年中的星期数（00-53）星期天为星期的开始|
| %w | 星期（0-6），星期天为星期的开始|
| %W | 一年中的星期数（00-53）星期一为星期的开始|
| %x | 本地相应的日期表示|
| %X | 本地相应的时间表示|
| %Z | 当前时区的名称|
| %% | %号本身|

## Reference
* [Python中的时间与日期](https://zhuanlan.zhihu.com/p/61767616)
* [Python日期和时间](https://www.runoob.com/python/python-date-time.html)
* [你想要知道的Python日期格式化知识都在这！](https://juejin.im/post/5a1bfab16fb9a045104a3702)
* [你真的了解Python中的日期时间处理吗？](https://zhuanlan.zhihu.com/p/23679915)