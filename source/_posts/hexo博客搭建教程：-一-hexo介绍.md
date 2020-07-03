title: hexo博客搭建教程：(一)hexo介绍

date: 2014-03-12 19:58:13

categories: hexo教程

tags: [教程,hexo]

---

以下是关于hexo的一些相关介绍。

<!--more-->

前些天看到一个学长的博客，感觉很棒，一直想拥有一个自己独立博客的我便开始研究起了hexo。博主虽然没有经历过jekyll、Octopress、WordPress的时代，但还是被hexo快速、简单且功能强大的Node.js博客框架打动了，支持markdown，这个逼格极高的程序猿写作方式，让我深深爱上了它，就连hexo的发音都有点黑客的味道。Hexo是基于Node.js的静态博客，出自台湾大学生tommy351之手。哎，大学生，博主同样是大学生，不过相比起来差距就是大，小小的悲伤一下，再振作起来奋斗吧。

**接下来说说为什么要用hexo**

* hexo生成的静态网站可以直接放到GitHub Pages，BAE，gitcafe等平台上。

* 搭建过程还是很简单的，不过由于需要使用Git、Markdown、Node.js这样的工具，许多插件都得自己安装和设置。可能第一次搭建的时候回觉得有点小繁琐，但是搭建完成之后使用起来是很简单的。

* 常用的命令就那么几个。

	```objectivec
	hexo n      //写文章
	hexo g      //生成
	hexo d      //部署
	hexo s      //本地查看
	hexo d -g   //生成-->部署，相当于hexo g与hexo d合并。
	hexo s -g   //生成-->本地查看，相当于hexo g与hexo s合并。
	```

**以下引用作者[@tommy351][1]的话**

> * A fast, simple & powerful blog framework, powered by Node.js. 

> * 快速、简单且功能强大的Node.js博客框架。

这是hexo的[官方网站](http://hexo.io/)。大家可以自己进去感受一下，突然发现学了几天，经受了几天的折磨，博主已经对英文网站免疫了^_^。

接下来就来说说怎样搭建你自己的hexo博客吧。

**PS：**本教程是使用hexo创建你的博客，并托管在gitcafe上的，为什么不用github呢，因为听说github被黑过。另外gitcafe这家网站是国内的，据说访问速度比github快多了，同时本教程只针对Windows用户，仅供Linux和Mac用户参考。

[1]: http://twitter.com/tommy351
