title: "本地搭建IPv6测试环境"

date: 2016-06-28 14:17:59

description:

categories: iOS开发 - 问题

tags: [iOS,审核,上线]

---

从6月初开始，苹果修改了App Store应用审核政策，规定所有提交审核的iOS应用都必须支持IPv6-only连网标准。虽然苹果早在去年的在WWDC2015大会上就宣布过iOS 9将转用IPv6-only网络服务，但是该消息来的依旧是如此的突然，导致大部分开发者毫无一丝防备。

<!--more-->

### 什么是IPv6

IPv6是Internet Protocol version 6的缩写，全名为互联网通讯协议第6版，是互联网协议的最新版本，用于分组交换互联网络的网络层协议，旨在解决IPv4地址枯竭问题。

IPv6采用128位的地址，而IPv4使用的是32位。相对于IPv4，IPv6具有更大的地址空间，使用更小的路由表，并且具有更高的安全性。

当然，以上只是官方对于IPv6的定义，不知道你们看完之后会不会露出，哦~原来如此的表情。那么IPv6具体长啥样？具体一点，IPv6就是我们看到的类似这样`2001:da8:215:4009:250:56ff:fe97:40c7 `的地址。而IPv4则是`114.255.40.66`这样的地址，是不是很熟悉。

目前来说，一个网卡会同时使用这两种地址，但在未来，一个网卡可能就仅支持IPv6地址， 并且使用这两种地址的设备是不能互相访问的，除了某些过渡的技术让他们在特定情况下可以互相访问，比如DNS64/NAT64。

### 如何兼容IPv6

其实在大多数情况下，我们并不需要做什么，就可以兼容IPv6。比如目前使用的最多的网络库AFNetworking3.0就已经支持IPv6，我们只需在开发中注意一下以下的细节并做出一些改动就可以了。

* 使用更高层次的网络API，避免使用socket API。
* 在你的应用中使用域名，不要在任何地方使用 IPv4地址。
* 参照IPv4->IPv6类型对照表，修改IPv4代码，删掉不兼容IPv6的代码。

	IPv4->IPv6类型对照表
	
	![](/img/iOS问题-本地搭建IPv6测试环境/IPv4->IPv6类型对照表.png)

	不兼容IPv6的API
	
		inet_addr()
		inet_aton()
		inet_lnaof()
		inet_makeaddr()
		inet_netof()
		inet_network()
		inet_ntoa()
		inet_ntoa_r()
		bindresvport()
		getipv4sourcefilter()
		setipv4sourcefilter()

### 搭建IPv6测试环境

如果想测试一下自己的应用是否支持IPv6，不如自己搭建一个测试环境。MacOS提供了创造IPv6-only测试环境的功能，操作很简单。

1. 首先我们将Mac连接网线上网，关闭Wifi。

2. 打开系统偏好设置，按住Option键打开共享。

	![](/img/iOS问题-本地搭建IPv6测试环境/搭建IPv6测试环境01.png)

3. 勾选创建NAT64网络，打开互联网共享，即使用Mac做一个Wifi热点。

	![](/img/iOS问题-本地搭建IPv6测试环境/搭建IPv6测试环境02.png)

4. 最后使用iPhone连接上这个刚创建好的热点就可以测试了。和平常创建Wifi热点的区别是，这次我们创建的是一个本地的IPv6 DNS64/NAT64网络。


