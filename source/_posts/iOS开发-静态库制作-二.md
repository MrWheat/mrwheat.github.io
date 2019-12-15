title: "iOS开发-静态库制作(二)"

date: 2016-05-24 15:28:40

description: 

categories: iOS笔记

tags: [iOS,开发,静态库]

---

本篇主要讲解关于.a静态库的制作和使用，封装之旅就此开始。

<!--more-->

###如何新建.a静态库

1. 创建工程，选择创建Framework&Library中Cocoa Touch Static Library。

	![](http://7pumug.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-05-24%2005.07.27%20PM.png)

2. 编写代码。

3. 生成lib库，将写好的代码分别在模拟器和iOS Device模式下运行。

 	注意: Show In Finder生成了2种, 分别在模拟器下运行和真机下运行。
 	
 	通过终端命令`lipo -info 库的路径`可以查看库的平台。 
 	 
 	真机: armv7 arm64
	
	模拟器: x86_64
	
4. 合并两种库，在终端输入：

	`ipo -create Debug-iphoneos/libMJRefresh.a Debug-iphonesimulator/libMJRefresh.a -output libMJRefresh.a`
	
	相当于
	
	`ipo -create 真机库文件路径 模拟器库文件路径 -output 合并后库文件路径`

5. 将头文件、资源和库放在一起，为了避免资源和工程资源冲突问题，最好将资源放在bundle里。

6. 使用的时候将头文件、资源和.a文件直接拖入工程即可。

###参考

[静态库的制作](http://www.cnblogs.com/wendingding/p/3893095.html)