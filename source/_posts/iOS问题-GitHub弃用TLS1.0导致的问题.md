title: "GitHub弃用TLS 1.0导致的`SSL23_GET_SERVER_HELLO:tlsv1`问题"

date: 2018-07-13 18:38:59

description:

categories: iOS开发 - 问题

tags:    [iOS]

---

**原因：**

本地`pod install`时出现了以下问题：

![](/img/iOS问题-GitHub弃用TLS1.0导致的问题/79752-7b84bc0ebd49ecb8.png)

<!--more-->

后来才发现是GitHub停止了对加密弱TLS 1.0和TLS 1.1协议的支持，并且仅支持与TLS 1.2协议的连接，如果你本地仍然使用TLS 1.0就会报错。

[官方声明](https://githubengineering.com/crypto-removal-notice/)

**解决方法：**

* 首先查看本地TLS版本，使用下面两个命令都可以。

```
git config --global --list
git config http.sslVersion
```

* 下载最新的Git版本，[下载传送门](https://git-scm.com)

* 下载安装完成以后，运行以下命令。

```

git config --global --unset http.sslVersion
git config --global --add http.sslVersion tlsv1.2

```

这个时候再执行`pod install`或者git相关的命令时就不会报错啦。

