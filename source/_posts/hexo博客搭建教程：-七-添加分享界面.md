title: hexo博客搭建教程：(七)添加分享界面

date: 2014-06-27 21:13:29

categories: hexo教程

tags: [教程,hexo]

---

有段时间没更了，懒癌晚期真心没得治，当然也有其他的原因。寒假的时候重装了一次系统，今天才又重新把环境搭好。问题挺多，以前的hexo教程跟现在可能有些出入，毕竟软件更新速度还是很快的，更新就意味着一些改变，当然，前文的不当之处已经更正，至少到今天为止还是适用的，请放心食用。

<!--more-->

今天介绍如何添加分享界面，就是博主博客右侧那个红白色的分享按钮。

在`hexo\themes\jacman\layout\_partial\baidu_tongji.ejs`的开头添加如下代码：

```
<% if (theme.baidu_share.enable){ %>
<script>
    <% if(theme.baidu_share.color&&theme.baidu_share.color>=0){ %>
        var _bdImg = '<%=theme.baidu_share.color%>';
    <% }else{ %>
        var _bdImg = "4";
    <% } %>
    window._bd_share_config={
        "common":{
            "bdSnsKey":{

            },
            "bdText":"",
            "bdMini":"2",
            "bdMiniList":[
                "qzone",
                "tsina",
                "weixin",
                "renren",
                "tqq",
                "tieba",
                "douban",
                "sqq",
                "diandian",
                "huaban",
                "youdao",
                "mail",
                "ty",
                "fbook",
                "twi",
                "linkedin",
                "copy",
                "print"
            ],
            "bdPic":"",
            "bdStyle":"0",
            "bdSize":"16"
        },
        "slide":{
            "type":"slide",
            "bdImg":_bdImg,
            "bdPos":"right",
            "bdTop":"350"
        },
        "image":{
            "viewList":[
                "weixin",
                "qzone",
                "tsina",
                "renren",
                "douban",
                "tqq"
            ],
            "viewText":"分享：",
            "viewSize":"16"
        },
        "selectShare":{
            "bdContainerClass":null,
            "bdSelectMiniList":[
                "weixin",
                "qzone",
                "tsina",
                "renren",
                "douban",
                "tqq"
            ]
        }
    };
    with(document)0[(getElementsByTagName('head')[0]||body).appendChild(createElement('script')).src='http://bdimg.share.baidu.com/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];
</script>
<% } %>
```

然后在`hexo\themes\jacman\_config.yml`中添加：

```
baidu_share:
  enable: true
  color: 4
```

如果有该选项，设置为true，打开就行。
