title: hexo博客搭建教程：(三)将你的博客托管到gitcafe平台上

date: 2014-03-22 20:02:34

categories: hexo教程

tags: [教程,hexo]

---

前面准备好之后就可以将你的博客托管到Gitcafe上了。

<!--more-->

### 1. 注册Gitcafe账号

已有账号的忽略，没有的，去[官网](https://gitcafe.com/)注册，全中文界面，很简单，这里就不介绍了。

![](/img/Gitcafe官网.png)

### 2. 创建添加SSH

在桌面，点击鼠标右键，进入Git Bash，会得到一个命令行界面，输入：

> cd ~/ .ssh

如果提示：No such file or directory 说明你是第一次使用git，先输入下方的命令手工创建一个.ssh目录。

> mkdir ~/.ssh

然后再执行：

> cd ~/ .ssh

否则跳过。执行下方的命令，将 `YOUR_EMAIL@YOUREMAIL.COM` 改为你的Email地址

> ssh-keygen -t rsa -C "YOUR_EMAIL@YOUREMAIL.COM"

然后去用户目录的.ssh目录下可以看到私钥 `id_rsa`和公钥 `id_rsa.pub`这两个文件，私钥是不能透露出去的，只能你自己知道。

用文本文件(txt)打开公钥`id_rsa.pub`，复制所有的内容。进入 `GitCafe` -->`账户设置`-->`SSH` 公钥管理设置项，添加新公钥。名称可以随意起，将公钥粘贴进去。

![](/img/gitcafe公钥添加.png)

以上步骤完成后，用下面的命令测试一下吧。

> ssh -T git@gitcafe.com

如果是第一次连接的话，会出现以下警告：

```objectivec
The authenticity of host 'gitcafe.com (50.116.2.223)' can't be established.
#RSA key fingerprint is 84:9e:c9:8e:7f:36:28:08:7e:13:bf:43:12:74:11:4e.
#Are you sure you want to continue connecting (yes/no)?
请检查一下显示的指纹是否跟我们提供的一致：84:9e:c9:8e:7f:36:28:08:7e:13:bf:43:12:74:11:4e。 如果不一致请联系你的网络管理员，检查是否有中间人攻击。
```

如果没有问题，输入 yes 按回车就可以了。

中间会提示你输入 passphrase 口令。

> Enter passphrase for key '/c/Users/USERNAME/.ssh/id_rsa':

最后，如果连接成功的话，会出现以下信息。

```objectivec
Hi USERNAME! You've successfully authenticated, but GitCafe does not provide shell access.
```

参考：

[如何新建SSH-Key](https://help.github.com/articles/generating-ssh-keys/)

[gitcafe添加SSH-Key](https://gitcafe.com/GitCafe/Help/wiki/%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85%E5%92%8C%E8%AE%BE%E7%BD%AE-Git#wiki)

[关于SSH简介及公钥和私钥的基本概念](http://blog.csdn.net/qimiguang/article/details/17427331)

### ３. 新建gitcafe项目

![](/img/新建项目.png)

点击新建项目，项目名与你的gitcafe账号名一致，其他可不填，项目必须是公开的项目。

![](/img/新建gitcafe项目.jpg)

进入GitBash，第一次进入在本地设置git时必须：

>  $ git config --global user.name "yourname"　//你的计算机名
>  $ git config --global user.email "yourmaill@yourmaili.com"　//填写自己的邮箱

官网有提示的按照官网的提示步骤来就行。

### ４.将本地博客托管到Gitcafe上

首先安装`hexo-deployer-git`

> npm install hexo-deployer-git --save

等待安装完成。

编辑hexo文件夹下的`_config.yml`文件，使用文本文件(txt)打开就行，不过看着可能有点难受╮(╯▽╰)╭，可以去这下个[Sublime Text 2](http://www.sublimetext.com/)编译器，支持各种编程语言和文件格式，当然也支持Markdown语法，可以用它写文章。

```objectivec
deploy:
  type: git
  repository: https://gitcafe.com/`MrWheat`/`MrWheat`.git
  branch: gitcafe-pages
```

`MrWheat`是博主的名(麦子先生)。把上方的`MrWheat`换成你自己在`Gitcafe的账号名`就行。

然后执行下列指令完成部署：

> hexo g

> hexo d

根据提示输入gitcafe的账号和密码，就部署成功了。

这两个指令的意思是：

> hexo g---->hexo generate  #生成 

> hexo d---->hexo deploy    #部署 

注册完gitcafe，会给你一个二级域名，是和gitcafe的空间绑定的。一般是www.`gitcafe的用户名`.gitcafe.com。

博主的是www.`mrwheat`.gitcafe.com。

登录你的gitcafe二级域名看看吧。

**PS:**精力有限，可能有些细节没有注意，如有问题，或者有什么不懂的，可以在下方留言。博主会看到的^_^。
