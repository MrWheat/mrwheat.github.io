title: hexo博客搭建教程：(六)hexo博客主题的优化 (下)

date: 2014-06-20 17:02:40

categories: 编程基础 - 博客

tags: [教程,hexo]

---

如果还对你的博客不满意，就接着往下面看吧。

<!--more-->

### 添加自定义页面

这里以添加`关于`界面为例，添加其他页面方法相同。

执行new page命令：

> hexo new page "about"

在`\hexo\source\`下会生成about目录，里面会有个`index.md`文件，直接使用`Sublime Text2`编辑就可以了，支持`markdown`语法。然后在`\hexo\themes\jacman\_config.yml`中`menu`下进行配置。

```python
menu:
  首页: /
  归档: /archives
  关于: /about
```

### 设置百度统计

因Google Analytics偶尔被墙，故用百度统计。

首先去百度统计注册账号，填写相关信息，获得百度统计代码。

然后在`\hexo\themes\jacman\layout\_partial`中新建文件`baidu_tongji.ejs`。如果有了就不用新建勒，然后添加如下代码：

```python
<% if (theme.baidu_tongji){ %>
<script type="text/javascript">
你的百度统计代码
</script>
<% } %>
```

最后编辑`\hexo\themes\jacman\_config.yml`文件，将百度统计打开：

> baidu_tongji: true

如果没有，增加该配置选项就行。

配置完成后，不出意外的话，在你的站点的每个页面的左上角都会看到一个百度LOGO。如果看着难受，可以在『百度统计首页->网站列表->获取代码->系统管理设置->统计图标设置->显示图标』，把那个勾去掉，没办法，广告无处不在。

### 导航栏添加RSS

* 安装RSS插件到本地：`npm install hexo-generator-feed`

* 开启RSS功能：编辑`hexo/_config.yml`，添加如下代码：

	```
	plugins:
	- hexo-generator-feed
	```

* 在站点添加链接：

	在`hexo\themes\jacman\_config.yml`中，编辑 `rss: /atom.xml`
	
	在`hexo\themes\jacman\layout\_partial\header.ejs`中，`<ul></ul>`之间，添加一样代码`<li> <a href="/atom.xml">RSS</a> </li>`

### 添加sitemap

sitemap的初衷是给搜索引擎看的，为了提高搜索引擎对自己站点的收录效果，我们最好手动到google和百度等搜索引擎提交sitemap.xml。

* 安装sitemap到本地：npm install hexo-generator-sitemap
 
* 开启sitemap功能：编辑hexo/_config.yml，添加如下代码：

	```
	plugins:
	- hexo-generator-sitemap
	```

* 你可以到[屈站长](http://www.sousuoyinqingtijiao.com/)提交你的站点给搜索引擎。

### 在文章中插入图片

使用markdown写文章，插入图片的格式为`![图片名称](链接地址)`,对于图片的链接地址该怎么写呢？

* 可以复制网上的图片网址添加进来。

* 使用本地路径：在`hexo\source`目录下新建一个`img`文件夹，将图片放入该文件夹下，插入图片时链接即为`/img/图片名称`。

* 或者使用图床，推荐用七牛，首先注册七牛账号，然后将图片上传到七牛的空间，最后将图片在七牛的外链地址复制下来即可。

七牛除了作为图床，也可以作为其他静态文件的存储空间，比如博主的博客的背景音乐便是放在了七牛的空间，相对于其他外链地址，访问速度还是快很多的。

### 404页面

404页面也是可以做很多事情的，如果想创建自己的404页面，直接在根目录下创建自己的404.html就可以，但是自定义404页面仅对绑定顶级域名的项目才起作用。对gitcafe的二级域名是不起作用的。使用hexo s本地调试也不起作用。这里就不介绍了。有兴趣的百度找找吧。

### 最后就是购买域名

买域名推荐去GoDaddy吧，在国外，不受国内各种干扰，同时支持支付宝付款，信誉、口碑都很好，网上优惠码一类的东西也挺多的。如果没有特别的要求，不愿折腾直接上国内买也行，看个人，博主是在GoDaddy买的，追求完美嘛。想买的去[godaddy域名购买](https://www.godaddy.com/about/godaddy-chinese.aspx?isc=bsfndom4&cvosrc=ppc.baidu)有32%的优惠（这不是打广告= =!）。

### 设置DNS

在Godaddy网站购买完域名后，Goddady会默认提供一个国外的dns服务商，访问速度来说还不错，没有特别多要求的话默认就好。

Godaddy的DNS解析设置参照：[Godaddy域名解析教程](http://www.51php.com/domain/13235.html)，这里就不过多介绍了。记录值或者IP地址填写gitcafe提供的IP地址。

![](\img\IP地址.png)

然后将你的域名添加到项目的自定义域名里就可以了。一个IP地址可以指向多个域名，而一个域名只能指向一个地址。

Godaddy域名DNS解析设置完成之后，一般过十分钟左右就能生效，网站就能正常登陆勒。

博主这里使用的是[dnspod](https://www.dnspod.cn/)提供的域名解析服务，使用dnspod的解析一个新的域名可能会遇到有电信或联通的网可以访问，其他的网不能访问的情况，这是因为运营商不同的缘故，由于各地的DNS刷新时间不同，没有刷新的DNS服务器会继续向旧的DNS服务器发送请求，从而造成错误的解析结果，出现这样的情况，一般等待24到72小时后，解析会恢复正常，至于为什么Godaddy那么快。因为你注册的时候本身就是用他们的DNS，不需要重新修改，各地也没缓存。所以不能正常登陆网页的博主们也不用太过担心。

关于hexo博客的搭建教程就写到这勒，优化永无止境的，暂时想到的就这么多，如有不当或者疑问的地方，可以指出，有时间会将DNSPOD域名解析教程以及添加背景音乐的教程写上，突然觉得肚子有点饿了。。。
