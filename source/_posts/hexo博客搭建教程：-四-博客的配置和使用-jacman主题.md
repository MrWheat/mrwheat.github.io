title: hexo博客搭建教程：(四)博客的配置和使用---jacman主题

date: 2014-04-2 20:03:53

categories: hexo教程

tags: [教程,hexo]

---

前几节结束，大家应该已经搭建好自己的博客勒，当然还没完工，还需要进行一些简单的配置。

<!--more-->

首先讲一下对整个站点的配置。这里用到的是<i>`\hexo\ _config.ym`</i>这个文件。可以使用[Sublime Text 2](http://www.sublimetext.com/)打开这个文件，按照下方的介绍进行配置：

```
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: MaiZi's Blog	#站点名，站点左上角
subtitle: 当你的才华撑不起你的野心的时候，你就应该静下心来学习！ #副标题，站点左上角
description: 学习 随笔 文章 #给搜索引擎看的，对站点的描述，可以自定义
author: 麦子-Wheat #在站点下方可以看到
email: 390823033@qq.com #你的联系邮箱
language: zh-CN #中国人嘛，用中文

# URL #这项暂不配置，绑定域名后，欲创建sitemap.xml需要配置该项
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://heymz.com
root: /
permalink: :year/:month/:day/:title/
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
permalink_defaults:

# Directory 不修改
source_dir: source
public_dir: public

# Writing 文章布局、写作格式的定义，不修改
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
highlight:
  enable: true
  line_number: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Archives 默认值为2，这里都修改为1，相应页面就只会列出标题，而非全文
## 2: Enable pagination
## 1: Disable pagination
## 0: Fully Disable
archive: 1
category: 1
tag: 1

# Server 不修改
## Hexo uses Connect as a server
## You can customize the logger format as defined in
## http://www.senchalabs.org/connect/logger.html
port: 4000
server_ip: localhost
logger: false
logger_format: dev

# Date / Time format 日期格式，不修改
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: MMM D YYYY
time_format: H:mm:ss

# Pagination 每页显示文章数，根据自己习惯，我将10改成了6
## Set per_page to 0 to disable pagination
per_page: 6
pagination_dir: page

# Disqus Disqus插件，我们使用多说，不修改
disqus_shortname:

#这里配置站点所用主题和插件，暂默认，后面会介绍怎么修改
#（别照博主的来，博主已经改过了，下面会介绍）
## Plugins: https://github.com/hexojs/hexo/wiki/Plugins
## Themes: https://github.com/hexojs/hexo/wiki/Themes  这是下载主题的网站
theme: jacman
exclude_generator:

# Extensions 默认，不改
plugins:
- hexo-generator-feed
- hexo-generator-sitemap
#Feed Atom
feed:
	type: Atom
	path: atom.xml
	limit: 20

#sitemap
sitemap:
path: sitemap.xml

# Deployment 站点部署到gitcafe要配置，上一节中已经讲过
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: github
  repository: https://gitcafe.com/MrWheat/MrWheat.git
  branch: gitcafe-pages
```
  
然后用`hexo s -g`命令，从网页打开`localhost:4000`查看一下效果吧，感觉不错吧，离胜利就要不远了。

这里说说网页访问速度慢的原因，原因是默认的主题`landscape`使用了google的在线字体还有google的服务。google被墙，根据Google透明度报告显示，从5月27日开始，Google的部分服务开始被屏蔽，其中最主要的是HTTPS搜索服务和Google登录服务，所有版本的Google都受到影响，包括Google.hk和Google.com等。打开浏览器，按F12，点击下方的Network，刷新一下，就能看到，到底是什么拖慢了网站访问的速度。当然，解决字体的方法也很简单。

1. 换一个对国内友好的主题就行了。

2. 如果你非得用landscape这个主题，可以把google字体屏蔽。在`/hexo/themes/landscape/source/css/_base`中找到 `variable.styl`删掉：

	```objectivec
	@import url("//fonts.google.com/css?family=Lato:400,400italic")
	```

3. 如果你非得用google的字体。去官网把字体下载下来吧，这里就不介绍了。或者在上面的代码中把` fonts.googleapis.com `替换成 `fonts.useso.com `。

步骤2和3解决起来略麻烦，因为除了字体还有google其他东西在作用，一个一个改起来还是很麻烦的，实在想用的可以在下方留言。这里主要讲方法1。

在hexo文件夹下使用git bash：

> $ git clone https://github.com/wuchong/jacman.git themes/jacman

> $ cd themes/jacman

> $ git pull

下载安装主题，这就是博主现在用的主题jacman，做了一点小改动。
安装完主题之后，修改下方theme为jacman。

```objectivec
#这里配置站点所用主题和插件，暂默认，后面会介绍怎么修改
#（别照博主的来，博主已经改过了，下面会介绍）
## Plugins: https://github.com/hexojs/hexo/wiki/Plugins
## Themes: https://github.com/hexojs/hexo/wiki/Themes  这是下载主题的网站
theme: jacman
exclude_generator:
```

`hexo s -g`去网页打开`localhost:4000`查看一下效果吧。也可以`hexo d -g`上传到`gitcafe`登录你的gitcafe二级域名查看。

当然从博客建成到现在我们还没发表过文章呢，该怎么写文章呢？

1. 新建一篇文章页面。

	```objectivec
	hexo n "文章标题"
	```

2. 在\hexo\source\_posts中打开这个文件，配置开头：

	```objectivec
	title: 文章标题 #这个是你的文章标题，默认跟文件名一致，可以改”
	date: 2013-05-29 07:56:29 #发表日期，自动生成不改动
	categories: hexo教程 #文章文类，不需要直接不填或者删了这一项就可
	tags: [教程] #文章标签，多于一项时用逗号隔开，不需要直接不填或者删了这一项就可
	description: 你对本页的描述，不需要直接不填或者删了这一项就可
	---
	#这里是正文，用markdown写
	```

	**PS：**title:、date:、categories:、tags:、description:后面记得加`空格!`
	
当然有些人会反映默认生成的样式里没有description:和其他的选项，你可以自己添加进去，也可以修改`\hexo\scaffolds\post.md`文件，下次使用`hexo n`的时候就会按照这个文件的样式来了。其他照片文章，生成页面默认改动方法相同，这里就不多说了。
	
```objectivec
hexo n photo "文章标题"  //生成照片文章。
hexo n page "页面标题"   //生成页面，跟生成`关于`一样，这个后面会讲。
```

关于Markdown的用法这里不介绍了，网上有很多，不懂的参考以下网站，很简单。

[轻松搞定Markdown](http://zipperary.com/2013/08/31/easy-markdown/)
[Markdown 语法速查表](https://gitcafe.com/GitCafe/Help/wiki/Markdown-%E8%AF%AD%E6%B3%95%E9%80%9F%E6%9F%A5%E8%A1%A8#wiki)
[Cmd - 在线 Markdown 编辑阅读器](https://www.zybuluo.com/mdeditor)

现在就赶紧写一篇文章试试吧。

下节将介绍怎么优化主题，可以不看，如有不当请指出。