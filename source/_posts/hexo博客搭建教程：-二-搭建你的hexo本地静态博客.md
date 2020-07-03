title: hexo博客搭建教程：(二)搭建你的hexo本地静态博客

date: 2014-03-15 20:01:36

categories: 编程基础 - 博客

tags: [教程,hexo]

---

现在来开始搭建属于你自己的博客吧，首先从安装工具开始。

<!--more-->

### 1. 安装node.js

到[node.js官网](http://nodejs.org/)下载安装文件，并一路Next即可。

### 2. 安装Git

Git的客户端很多，我用的是[msysgit](http://code.google.com/p/msysgit/)，同样下载下来，一路Next即可。官网被墙上不去的[戳这](http://xiazai.xiazaiba.com/Soft/M/msysgit_1.9.4.0_XiaZaiBa.zip)进行下载。

### 3. 安装hexo

任意位置点击鼠标右键，选中Git Bash进入命令行界面。输入以下命令：

```objectivec
npm install -g hexo
```

等待就行，安装完成就关掉吧。

### 4. 安装hexo文件夹

进入你想放你博客的位置，新建hexo文件夹，我的是D:\hexo，然后进入你新建的hexo文件夹，点击鼠标右键选择Git Bush（`之后的命令全在此文件夹下使用Git Bush`）。

```objectivec
hexo init
```

### 5. 安装依赖包

```objectivec
npm install
```

以上做完之后我们就可以搭建自己的本地hexo静态博客了。进入Git Bush(`记住，以后都在hexo文件夹下进入`)，执行以下命令：

```objectivec 
hexo g  hexo g // hexo generate 生成静态博客  
hexo s  hexo s // hexo server 本地查看
```

浏览器输入`localhost:4000`或[戳这](http://localhost:4000/)就可以查看你刚刚生成的博客勒，不过是本地的哦。

**PS：**可能打开网站的速度有点慢，请耐心等待。这个原因后面会讲，完成之后先跟着教程走吧。

### hexo语法速查：

> * hexo g 　　　#生成 
> * hexo d 　　　#部署 
> * hexo n 　　　#写文章
> * hexo s 　　　#本地查看 
> * hexo d -g 　　#生成-->部署，相当于hexo g与hexo d合并。 
> * hexo s -g 　　#生成-->本地查看，相当于hexo g与hexo s合并。
