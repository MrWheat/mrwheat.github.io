title: hexo博客搭建教程：(五)hexo博客主题的优化 (上)

date: 2014-05-15 15:27:45

categories: hexo教程

tags: [教程,hexo]

---

好多天没更了，今天把那个博客教程系列写完吧，忙完这阵又可以忙下一阵了，╮(╯▽╰)╭。

<!--more-->

这节主要讲一些关于博客主题的一些优化，插件以及一些功能扩展。因为博主用的是jacman主题，便在这个主题上进行讲解勒，其他主题修改的方式大同小异，因为文件结构都差不多，自己参照这个系列教程也能琢磨明白，很简单。想换主题的可以去[hexo主题下载](https://github.com/hexojs/hexo/wiki/Themes)选择自己喜欢的进行修改。

###添加作者信息

关于主题的配置，主要是用到`\hexo\themes\jacman`下的`_config.yml`文件。

```objectivec
author:
  intro_line1:  "In my solitude of heart I feel the sigh of this widowed evening veiled with mist and rain."  ## 网站下方第一行显示的内容
  intro_line2:  "这寡独的黄昏，幕着雾与雨，我在我心的孤寂里，感觉到它的叹息。"  ## 网站下方第二行显示的内容
  weibo: 3202240184   ## 添加微博主页信息，我的是http://weibo.com/u/3202240184，就填3202240184。
```

如果想修改照片，直接在\hexo\themes\jacman\source\img下将照片替换就好，注意名称一致。

非jacman主题跳过这段。

###添加“多说”评论

评论系统这里选择国内挺好用的“多说系统”。步骤很简单：

* 首先去[多说](http://duoshuo.com/)官网进行注册，创建站点，获得多说的通用代码。

* 将通用代码粘贴到`hexo\themes\jacman\layout\_partial\post\comment.ejs`里面，如下：

	```python
	<% if ( page.comments){ %>
	<section id="comment">
	通用代码
	</section>
	<% } %>
	```

* 然后修改通用代码，将其中的。

	```python
	<!-- 多说评论框 start -->
		<div class="ds-thread" data-thread-key="请将此处替换成文章在你的站点中的ID" data-title="请替换成文章的标题" data-url="请替换成文章的网址"></div>
	<!-- 多说评论框 end -->
	
	替换为：
	
	<!-- 多说评论框 start -->
		<div class="ds-thread" data-thread-key="<%= page.path %>" data-title="<%= page.title %>" data-url="<%= page.permalink %>"></div>
	<!-- 多说评论框 end -->
	```
	
* 然后修改`hexo\themes\jacman\_config.yml`，打开“多说”评论，关掉“disqus”评论系统。

	```python
	duoshuo: 
	  enable: true  ## ture打开
	  short_name:  mrwheat  ## 你的多说二级域名，我的是：http://mrwheat.duoshuo.com，所以就填mrwheat。

	disqus:
	  enable: false  ## false关闭
	  short_name:    ## 不使用，不填
	```

	你可以在多说的后台管理界面管理你的博客留言以及进行一些相关设置。

###添加分类、标签、云标签

在`hexo\themes\jacman\_config.yml`中的`widgets`下添加：

```python
widgets:
- tag 
- category
- tagcloud
```

根据自己喜好添加吧，category：分类，tag：标签，tagcloud：云标签，添加的顺序跟右侧页面显示的顺序是一样的。

###添加友情链接

同样在`hexo\themes\jacman\_config.yml`中的`widgets`下添加：

```python
- links
```

然后去`\hexo\themes\jacman\layout\_widget\links.ejs`下修改你要在`友情链接`显示的内容和链接地址，使用html标签，不支持markdown。

###添加个人介绍

同样在`hexo\themes\jacman\_config.yml`中的`widgets`下添加：

```python
- blogroll
```

然后去`\hexo\themes\jacman\layout\_widget`新建blogroll.ejs文件。添加如下内容：

```python
<div class="widget tag">
<p class="asidetitle"><%= __('博主简介') %></p>
<ul class="entry">
<li>代号：麦子/MaiZi</li>
<li>现状：正在JAVA的路上努力奋斗ing。</li>
<li>QQ号：390823033</li>
<li>想交朋友的请<u><i><b><a href="http://mrwheat.gitcafe.com/about/" title="about me" style="color:red">联系我</a></b></i></u>！欢迎美女来搭讪^_^！</li>
</ul>
</div>
```

使用html标签，自己根据需要改吧，如果还想在左侧添加其他版块，添加方法与此方法相同。

###添加新浪微博秀

* 去[新浪微博开放平台](http://open.weibo.com/widget/weibotopic.php)根据自己喜好设置和生成微博秀代码。

* 在`\hexo\themes\jacman\layout\_widget`中新建名为`weibo.ejs`的文件，如果有了就不用新建勒。然后添加如下代码:
	
	```python
	<div class="weiboshow">
	  <p class="asidetitle"><%= __('weibo') %></p>
	 微博秀代码
	</div>
	```
	
* 在`\hexo\themes\jacman\_config.yml`中的`widgets`下添加：
	
	```python
	- weibo
	```

突然发现，优化是永无止境的，许多细节大家可以自己尝试一下，这节就先介绍这么多了。