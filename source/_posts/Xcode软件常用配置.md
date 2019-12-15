title: "Xcode软件常用配置"

date: 2016-05-26 15:30:11

description:

categories: Xcode配置

tags: [编译器,配置,Xcode]

---

俗话说，好记性不如烂笔头，特别在项目工期长的时候，写一个项目几个月，有些配置基本上配置一次就行了，长时间不使用，不管用的多熟，还真有忘记的可能。于是就花了一点时间整理一下，持续更新补充。

<!--more-->

* 头文件搜索路径设置（Header Search Paths）

	在`Bulid Settings-->Search Paths-->Header Search Paths`中添加搜索路径。`$(SRCROOT)`宏和`$(PROJECT_DIR)`宏都指`xxx.xcodeproj`所在目录。
	
* 配置PCH文件

	在`Bulid Settings`中搜索Prefix，找到`Prefix Header`设置选项，然后将PCH文件路径添加进去。
	
* 关闭ARC自动引用计数
	
	在`Bulid Settings`中搜索Automatic，找到`Objective-c Automatic Reference Counting`设置选项，然后将其置为NO。
	
* ARC和MRC混编

	在`Build Phases-->Compile Sources`中找到你要手动(自动)管理的文件，双击，在方框中输入相应的内容。
	
	> 如果你的工程是开启ARC的，那就需要对某些文件禁用ARC(-fno-objc-arc)
	
	> 如果你的工程是关闭ARC的，那就需要对某些文件开启ARC(-fobjc-arc)
	
* 设置App名字

	在工程的`info.plist`文件中添加Key`Bundle display name`，类型为String，Value为自定义名字。
	
* 使用地图定位

	如果App中需要使用地图定位，则需要在工程的`info.plist`文件中添加Key`NSLocationAlwaysUsageDescription`，类型为String，Value为你想要提示的内容。