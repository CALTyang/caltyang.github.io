---
title: Homebrew是如何工作的
date: 2016-04-29 19:28:55
tags: OSX
---

。Homebrew 是 OSX 平台下一个很好的包管理器
。如果是命令行玩家，或者用过 Linux 之类的，对 apt、yum 等也不陌生，homebrew 能够极大的简化我们安装三方工具的步骤，高效便捷
。当然 Homebrew 还不止步于命令行工具，通过homebrew-cask ，甚至可以将包管理拓展到 GUI 应用，比如我需要安装一个新的 chome，直接 cask install chrome 即可，科技范十足
。本文记录 Homebrew 的一些概念，工作方式等等
<!--more-->
## 0x01 几个 homebew 的命令
***
1.brew install/remove
> 。安装和卸载 package，和 apt-get install/uninstall 类似

2.brew info/desc
> 。查看一个 package 的基本描述和详细信息

3.brew doctor
> 。检查，比如package是不是最新，软连接是否正确，有没有一些配置的问题之类

4.brew link/unlink
> 。用来重新构建一个命令的软连接和取消软连接

5.brew update 和 brew upgrade
> 。update 是更新 homebrew 的 formula 表，其实就是进行一个 pull request，和远程仓库进行统一
> 。upgrade 则是实际的进行 package 的更新

## 0x02 应用是如何安装的
***
。我们知道 brew 安装的软件默认都在 /usr/local/Cellar 文件夹下
。而其实 homebrew 的安装目录也是在 /usr/local 下的，homebrew 会在该目录下初试 git 环境，然后在该目录下维护和track包之类，查看该目录下的 git 配置可发现

。默认安装的 Formula 在 /usr/local/Library/Formula 文件夹下
。我们已 cloc（一个代码统计的工具）为例，它的formula如下：
```ruby
class Cloc < Formula
  desc "Statistics utility to count lines of code"
  homepage "https://github.com/AlDanial/cloc/"
  url "https://github.com/AlDanial/cloc/archive/v1.66.tar.gz"
  sha256 "6d8b5e9fac8934478a0fd815f60d814b93d4a12344c4820f5b47a55048b21d53"
  head "https://github.com/AlDanial/cloc.git"
  bottle do
    cellar :any_skip_relocation
    sha256 "c140cd2b7244b48e45971130d69ffa3a250d7fda4885098b6a01a33fb3bc4ef1" => :el_capitan
    sha256 "4abbd7aec11e52eac56a1ab969a3825ff162f7d19598b94c02116dc01ad18e43" => :yosemite
    sha256 "994e73fb9afde00c2f3fde26a9421707bf73ce85de6d7021d4db2819307fbd48" => :mavericks
  end
  def install
    system "make", "-C", "Unix", "prefix=#{prefix}", "install"
  end
  test do
    (testpath/"test.c").write <<-EOS.undent
      #include <stdio.h>
      int main(void) {
        return 0;
      }
    EOS
    assert_match "1,C,0,0,4", shell_output("#{bin}/cloc --csv .")
  end
end
```
。细心的同学可能就发现了，这里的一些信息其实和 brew desc 和 brew info 时是一致的，比如：

。所以，当我们安装 cloc （通过 brew install cloc）时，首先，homebrew 会使用 curl 来获取文件，并放在缓存目录中
。然后出于安全等因素考虑，会计算 checksum，并和sha256那个作比对
。如果文件下载正确的话，接下啦 homebrew 会解压归档到一个临时文件夹，然后执行 install 命令，比如这里就是下面的命令
```
make -C Unix prefix=/usr/local/Cellar/xxx install
```
。当安装顺利，homebrew 会建立一个 /usr/local/cellar/xxx/bin 下命令的软连接到 /usr/local/bin 以确保你可以直接在命令行中执行该命令
。当然，同样会建立到 lib 和 man 的软连接
。安装完成后，你也可以使用 brew test 命令来测试安装
。test 命令会建立一个临时文件夹，然后建立一个 sandbox，之后执行 formula 中的test方法

## 0x03 几个模糊的概念
***
1.brew tap
> 。这个其实类似 Debian 系列 APT 中的 PPA
> 。可以用来拓展 Homebrew 的仓库，使其覆盖更多的应用
> 。直接输入 brew tap 时，我们就可以看到本地 clone 了几个仓库
> 。参考： github-brew tap

2.formula
> 。简单的理解就是上面的 rb 文件
> 。用来定义安装，测试，定义应用的信息等

3.bottles
> 。内建的 formula
> 。在 homebrew 服务器上以 tag.gz 的形式存在

4.DSL
> 。DSL 是特定领域语言的缩写，一个简单的理解就是不想Java、C之类通用语言一样，DSL目标是实现特定的功能，比如包的管理，所以不必像其他领域一样，考虑太多的通用性和复用性，DSL中往往有很多扩展的语法、命令之类，以实现特定的功能
> 。比如 Gradle 中可以使用 Groovy 来实现 DSL

## Homebrew cask
***
。参考：[简洁优雅的Mac OS X软件安装体验 - homebrew-cask](http://ksmx.me/homebrew-cask-cli-workflow-to-install-mac-applications/)

## homebrew service
***
。我们知道有些 Linux 中可以通过 service 命令来开启关闭一些服务，比如 Nginx，MySQL 之类
。OSX 中也可以，不过是使用 launchctl
。Homebrew service 其实就是意在提供一个统一的 service 方式来管理服务
。参考：[github-homebrewService](https://github.com/Homebrew/homebrew-services)

## cakebrew
***
。这是一个图形化的 homebrew 工具
。可以用来可视化的安装，卸载，管理 package 等


## Reference
***
[How does Homebrew work internally?](https://www.quora.com/How-does-Homebrew-work-internally)
[What is the history of Homebrew?](https://www.quora.com/What-is-the-history-of-Homebrew)
[Homebrew 隐藏命令](https://icyleaf.com/2014/01/homebrew-hidden-commands/)
[Homebrew: OS X Package Management](http://dghubble.com/blog/posts/homebrew-os-x-package-management/)
