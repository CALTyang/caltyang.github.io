---
title: XPath
date: 2019-10-06 22:24:19
tags: Python
---

[TOC]

全称XML Path Language，是一门在XML文档中查找定位信息的语言。常用于爬虫进行信息的抽取。
> XPath 于 1999 年 11 月 16 日 成为 W3C 标准，它被设计为供 XSLT、XPointer 以及其他 XML 解析软件使用，更多的文档可以访问其[官方网站](https://www.w3.org/TR/xpath/all/)

常用浏览器中经常带有直接复制XPath的功能：
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190801/xpath-demo.png)

> 注：不同浏览器中拷贝出来的XPath值可能不一致

<!--more-->
## 语法、表达式
| 表达式 | 说明 |
|---|---|
| article | 选取所有article元素的所有子节点 |
| /article | 选取根元素article |
| article/a | 选取所有属于article的子元素的a元素 |
| //div | 选取所有div子元素（不论出现在文档任何地方） |
| article//div | 选取所有属于article元素的后代的div元素，不管它出现在article之下的任何位置 |
| //@class | 选取所有名为class的属性 |

### 谓语
| 表达式 | 说明 |
|---|---|
| /article/div[1] | 选取属于article子元素的第一个div元素 |
| /article/div[last()] | 选取属于article子元素的最后一个div元素 |
| /article/div[last()-1] | 选取属于article子元素的倒数第二个div元素 |
| //div[@lang] | 选取所有拥有lang属性的div元素 |
| //div[@lang='eng'] | 选取所有lang属性为eng的div元素 |

### 其他语法
| 表达式 | 说明 |
|---|---|
| /div/* | 选取属于div元素的所有子节点 |
| //* | 选取所有元素 |
| //div[@\*] | 选取所有带有属性的div元素 |
| /div/a \| //div/p | 选取所有div元素的a和p元素 |
| //span \| //ul | 选取文档中的span和ul元素 |
| article/div/p \| //span | 选取所有属于article元素的div元素的p元素，以及文档中所有span元素 |

## 常用类库
### Python
* lxml

## Reference
* [学爬虫利器XPath,看这一篇就够了](https://zhuanlan.zhihu.com/p/29436838)
* [xpath路径表达式笔记](http://www.ruanyifeng.com/blog/2009/07/xpath_path_expressions.html)