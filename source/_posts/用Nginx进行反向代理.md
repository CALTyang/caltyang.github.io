---
title: 用Nginx进行反向代理
date: 2017-12-04 14:12:01
tags: 架构
---

这几天整了个博客，用的 Hexo，托管在了 GitCafe 和 GitHub 上，都是 Pages 服务，虽然 Github 可能比格更高一点，但是比较国内还是 GitCafe 比较快，所以干脆 Hexo 中配置了两个仓库，每次写完hexo d 可以都推上去，Cafe 如果挂了，还能去 Hub 上看看~ 俩地址：GitCafe、GitHub，Hexo 的 config 配置如下：
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/hexoconfig.png)

<!--more-->
Hexo 建站的过程可以参考唐巧的博客[将博客从GitHub迁移到GitCafe](http://blog.devtang.com/blog/2014/06/02/use-gitcafe-to-host-blog/),或者国内某大神的[GitHub+Hexo搭建独立博客](http://coderec.cn/2015/11/24/GitHub-Hexo%E6%90%AD%E5%BB%BA%E7%8B%AC%E7%AB%8B%E5%8D%9A%E5%AE%A2/)。

前不久无意间看到了阿里云ECS学生特价，广告，没忍住就剁了一次手。(╯-╰)/ 然后首先装了个 Tomcat ，监听 80 端口，访问，结果就这样了：
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/hehe.png)

但是比较奇怪的是，阿里云是封了80端口好像，8080等其他自带的依旧可以访问，故想到可以拿 Nginx 做个反向代理，，于是弄了两个AWS的EC2实例，其中一个搭了Nginx，尝试把 tomcat.caltyang.cn 反向代理回阿里云的 ECS，不过是 8080 端口而已。记录如下：

## 0x01 安装 Nginx
***
。好吧，废话了，，，Ubuntu 下面直接apt-get，OSX 下面brew install就行
。主要是 OSX 下面可能会遇到安装后 403 Forbidden 的问题，可能原因如下：
1 html 目录（包括到html的所有文件夹）没有读的权限，尝试 chmod
2 nginx.conf 配置 user 为root owner（注意真的是 root owner）
。注意如果 OSX 下使用的是 Homebrew 安装的，默认配置路径在/usr/loca/etc/nginx下，html 路径在/usr/local/Cellar/nginx/版本/html下
。配置 user 如图：
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/nginxuser.png)

## 0x02 配置域名解析到 EC2
***
。因为在万网买的域名，直接在万网控制面板配置就行了：[其实我换 DNSPod 了]
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/dns.png)

。如图，我配置了一个tomcat.caltyang.cn，这是在我那台学生特惠的阿里云ECS上的，用的默认端口 8080 ，如果直接配 80 去访问，则会是上面那个有关部门的效果~，所以下面用 Nginx 来进行反向代理的配置

## 0x03 配置 Nginx
***
。其实这步没啥讲的，因为 Nginx 配置其实挺简单地，Ubuntu 下面直接改 /etc/nginx/default-sites/default 文件就行，加个 server
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/nginxconfig.png)

listern : Nginx 监听的端口号，80，不解释
server_name : 可以理解就是上面想让用户访问的域名，如我的 tomcat.caltyang.cn
proxy_pass : 实际跳转的机器，因为实际 Tomcat 是在 阿里云的ECS 上的，自己之前配了个 host.caltyang.cn 的映射，所以直接写成 http://host.caltyang.cn:8080 就行了，效果如图：
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/tomcat.png)

。重启一下 Nginx，不出意外，就可以直接通过 http://tomcat.caltyang.cn 来访问自己 ECS 上面的 Tomcat，绕过了80的限制，，，当然，多了一层路由，速度肯定有影响，，何况 Nginx 部署的 EC2 服务器还在美国

## 0x04 理论知识
***
。其实博客到这里已经结束了，，不过为了显得内容多，加点反向代理的理论知识而已

。问题一：什么是反向代理？
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20151204/proxy.jpg)

> 上面分别是正向代理和反向代理的拓扑图吧，代理我就不解释了，正向代理就好比我们每次需要访问一个网站，请求都会先到 proxy，然后由 proxy 到目标主机，大部分情况下，这个 proxy 和你自己的主机其实是在一个网段的，目标主机相互独立，没有什么关系。想法，反向代理则不是，大部分时候，proxy 和各个服务器在一个网段，比如各个服务器分别部署了不同的服务（jar包），你访问不同的服务器，都先到 proxy，然后由 proxy 去找服务器，就类似我上面的 Tomcat 例子，不过 ECS 和 EC2 其实是应该在一个网段的。
> 。也可以参考知乎这个：[反向代理是什么意思？](https://www.zhihu.com/question/24723688)

。问题二：反向代理和正向代理的区别？
> 参考：
> [什么是反向代理，如何区别反向与正向代理](http://blog.csdn.net/shixing_11/article/details/7106241)
> [正向代理和反向代理_百度经验](https://jingyan.baidu.com/article/f54ae2fcd895b81e93b84973.html)

。问题三：反向代理 和 负载均衡 的模型类似，两者的区别？
> 其实两个模型确实差不多，不过反向代理偏向实现，而负载均衡偏向用途，反向代理可是实现负载均衡，达到效果，但是也能做一些其他的，比如缓存，加快访问速度。
> 。参考：[nginx的反向代理和负载均衡的区别是什么](http://www.oschina.net/question/126236_119223) ←_← 中评论
