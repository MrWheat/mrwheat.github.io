title: "macOS 10.15 Catalina 升级问题"

date: 2020-08-06 19:19:23

description:

categories: iOS开发 - 问题

tags: [Pod,Mac]

---

因为工作需要，前段时间都在做Android开发，当然也有一些零碎的iOS需求，但是因为不涉及到发版等，所以Xcode都是能用就行。

最近在上传ipa包时，遇到了点问题，要求必须使用最新版本的Xcode进行打包，于是点开App Store去升级，发现还需要将macOS升级到最新版本，否则无法更新Xcode，没办法，更新吧，漫长的等待...

本以为等等就完事了，没想到还是有一些改变的，遇到的第一个问题就是，我的Pod不能用了。

<!--more-->

```
$ pod install
zsh: /usr/local/bin/pod: bad interpreter: /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/bin/ruby: no such file or directory

```

一开始以为是更新系统后Cocoapods被干掉了，重装的时候发现还在，猜测是新版本更新系统，目录发生改变导致的。

### Cocoapods更新问题

重装Cocoapods比较简单，使用gem或者brew安装都可以：

> gem install cocoapods

或

> brew reinstall cocoapods

这里使用的是第一种方式，直接执行安装pods命令：

```
$ sudo gem install cocoapods -n /usr/local/bin
Building native extensions. This could take a while...
ERROR:  Error installing cocoapods:
    ERROR: Failed to build gem native extension.
    ......

```

看起来是gem的问题，由于这些第三方依赖包都是通过gem管理的，所以先更新一下gem：

```
$ sudo gem update --system
ERROR: While executing gem ... (TypeError)
    no implicit conversion of nil into String.
    ......

```

发现有文件缺失，因为安装pods和更新gem都需要依赖ruby环境，猜测是ruby版本过低导致的，毕竟已经记不清上一次更新ruby是何年月了，于是跑去升级ruby。

升级过程中才发现，因为Mac系统自带ruby，貌似我的电脑上并没有安装过rvm，何为rvm？rvm是用来管理ruby版本的，没办法，装吧，执行rvm安装命令。

```
$ curl -L https://get.rvm.io | bash -s stable
ERROR: Failed to connect to raw.githubusercontent.com port 443: Connection refused

```

嗯，果然，人生不如意，十有八九，无论如何，生活还是要继续，网上搜索了一下，说是Github的一些域名的DNS解析被污染，导致DNS解析过程无法通过域名取得正确的IP地址。

打开 [https://www.ipaddress.com/](https://www.ipaddress.com/) 输入访问不了的域名: 

![](/img/iOS问题-macOS10.15升级问题/20200806181112.jpg)

查询之后可以获得正确的IP地址，然后在本机的host文件中添加，建议使用 [switchhosts](https://github.com/oldj/SwitchHosts/releases) 方便host管理.

![](/img/iOS问题-macOS10.15升级问题/20200806181420.jpg)

网络问题解决了，重新执行rvm安装命令，因为安装rvm需要借助另外一个工具Homebrew，Homebrew是一款Mac OS平台下的软件包管理工具，在安装的过程中会执行 `brew update` 进行升级。

```
$ brew update
Error: /usr/local is not writable. You should change the
ownership and permissions of /usr/local back to your
user account:
  sudo chown -R $(whoami) /usr/local

```

嗯，又遇到了这个权限的问题，那我们自己动手安装升级一下Homebrew吧，如果遇到443网络问题，可以参考前面提到的方式解决，网上也有说重新安装 xcode-select，自己试了下感觉还是不靠谱。

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

```

![](/img/iOS问题-macOS10.15升级问题/20200720121725.jpg)

安装成功之后，执行：

```
$ curl -L https://get.rvm.io | bash -s stable
$ rvm install ruby-2.6
$ rvm use ruby-2.6.3
$ rvm --default use 2.6.3
$ rm '/usr/local/bin/pod'
$ rm '/usr/local/bin/xcodeproj'
$ brew reinstall cocoapods

```

![](/img/iOS问题-macOS10.15升级问题/20200720122815.jpg)

到这里，问题基本上解决了。

### Mac写入保护权限

安装过程中，我们会遇到读写权限的问题，原因是Rootless机制，虽然通过上面的方式已经解决了，但我们还是简单了解一下。

```
$ sudo chown -R $(whoami) /usr/local
chown: /usr/local: Operation not permitted

```

苹果从 OS X El Capitan 10.11 系统开始使用了 Rootless 机制，系统默认将会锁定 /system、/sbin、/usr 这三个目录，用户要获取这三个目录的写权限，需要关闭Rootless。

通过`csrutil status`命令，可以查看该机制的运行情况，如需关闭：

* 重启Mac
* 开机时后按下 Command+R，进入恢复模式
* 在上面的菜单实用工具中找到并打开 Terminal
* 输入`csrutil disable`

打开的方式也是一样：

* 重启Mac
* 开机时后按下 Command+R，进入恢复模式
* 在上面的菜单实用工具中找到并打开 Terminal
* 输入`csrutil enable`

### 相关帖子

[rvm.io](https://rvm.io/rvm/install)
[Failed to build gem native extension](https://github.com/CocoaPods/CocoaPods/issues/9538)
[Failed to build gem native extension. Header file missing](https://github.com/CocoaPods/CocoaPods/issues/9602)
[Brew based CocoaPods CLI fails in MacOS 10.15 Catalina due incorrect fallback to Ruby 2.3](https://github.com/CocoaPods/CocoaPods/issues/8955)
[Failed to connect to raw.githubusercontent.com port 443: Connection refused](https://github.com/hawtim/blog/issues/10)
[MacOS 10.15.x 最新安装 Cocoapods 教程及问题解决](https://www.cnblogs.com/shisishao/p/12553221.html)


