title: "Xcode6升级到Xcode7的http接口异常问题"

date: 2015-9-23 10:52:56

description:

categories: 疑难杂症

tags: [iOS,开发,问题]

---

在Xcode7.0及以上版本中编译iOS APP时，默认会使用iOS9的一项新特新，使得所有http连接被禁用，项目里使用的API如果没有https的支持，就悲剧了。对ShareSDK来说，具体的表现可能就是，无法授权、分享、获取用户信息等。

<!--more-->

###http协议异常问题

原先一些Xcode6的版本中编写的代码，放到Xcode7中运行一下，会出现这样的`App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure. Temporary exceptions can be configured via your app's Info.plist file.`异常警告。当然程序还是能运行的，但是好像你的界面里没有数据。然后你开始回忆，漫长的回忆，最终得出，在升级Xcode前一切代码都是好使的。然后你开始注意到上面的这行语句。你查了官方文档，它这样告诉你：

App Transport Security

App Transport Security (ATS) enforces best practices in the secure connections between an app and its back end. ATS prevents accidental disclosure, provides secure default behavior, and is easy to adopt; it is also on by default in iOS 9 and OS X v10.11. You should adopt ATS as soon as possible, regardless of whether you’re creating a new app or updating an existing one.

If you’re developing a new app, you should use HTTPS exclusively. If you have an existing app, you should use HTTPS as much as you can right now, and create a plan for migrating the rest of your app as soon as possible. In addition, your communication through higher-level APIs needs to be encrypted using TLS version 1.2 with forward secrecy. If you try to make a connection that doesn't follow this requirement, an error is thrown. If your app needs to make a request to an insecure domain, you have to specify this domain in your app's Info.plist file.

一句话就是：用https协议。然而目前大部分的社交平台的接口并不支持https协议，所以上面这段英文看起来就存粹像给人添堵。那么如何解决呢？

在项目的info.plist中添加一个Key：App Transport Security Settings/NSAppTransportSecurity，类型为字典类型。

然后给它添加一个Key：Allow Arbitrary Loads/NSAllowsArbitraryLoads，类型为Boolean类型，值为YES。

就像下面这样：

![](/img/Xcode异常.png)

网上已有类似的文章，这里就不多做叙述了：

[Xcode7的适配问题](http://www.jianshu.com/p/e66927954c96)

###什么是https协议

HTTPS(Secure Hypertext Transfer Protocol)安全超文本传输协议 它是一个安全通信通道，它基于HTTP开发，用于在客户计算机和服务器之间交换信息。它使用安全套接字层(SSL)进行信息交换，简单来说它是HTTP的安全版。 它是由Netscape开发并内置于其浏览器中，用于对数据进行压缩和解压操作，并返回网络上传送回的结果。HTTPS实际上应用了Netscape的安 全全套接字层(SSL)作为HTTP应用层的子层。(HTTPS使用端口443，而不是象HTTP那样使用端口80来和TCP/IP进行通信。)SSL使 用40 位关键字作为RC4流加密算法，这对于商业信息的加密是合适的。HTTPS和SSL支持使用X.509数字认证，如果需要的话用户可以确认发送者是谁。

###http协议和https协议的区别

https协议需要到ca申请证书，一般免费证书很少，需要交费。 http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议 http和https使用的是完全不同的连接方式用的端口也不一样,前者是80,后者是443。
http的连接很简单,是无状态的 HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议 要比http协议安全。


###写在后面的话

写了这么多，默认禁止http协议，还是有些不能理解，相关测试数据表明使用HTTPS协议传输数据的工作效率只有使用HTTP协议传输的十分之一。假如为了安全保密，将一个网站所有的Web应用都启用SSL技术来加密，并使用HTTPS协议进行传输，那么该网站的性能和效率将会大大降低，而且没有这个必要，因为一般来说并不是所有数据都要求那么高的安全保密级别，所以还是建议，我们只需对那些涉及机密数据的交互处理使用HTTPS协议，这样就做到鱼与熊掌兼得。总之不需要用https的地方,就尽量不要用。




