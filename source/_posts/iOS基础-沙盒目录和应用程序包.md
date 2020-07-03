title: "沙盒目录和应用程序包"

date: 2016-07-25 17:20:09

description:

categories: iOS开发 - 基础

tags: [数据,沙盒,iOS]

---

iOS应用程序的所有数据都是保存在沙盒目录的，那么什么是沙盒目录？它的作用是什么？

每个应用程序存储在手机上，这些程序都是由苹果系统去管理的。为了防止应用程序相互破坏，相互影响，苹果为每个应用程序，创建了一个保险箱。一个程序在一个保险箱里，这个保险箱就是沙盒目录。 

它的作用是：

1. 其他应用程序无法访问该沙盒目录
2. 该应用程序也无法访问其他沙盒目录

也就是说，在苹果手机上的两个应用，他们的数据是不能共享的，你再也无法使用迅雷下载一个视频然后使用优酷打开播放。当然，我们可以通过苹果官方提供的一些有限接口，来访问相册，电话簿，短信, 地理位置等应用，甚至在被授权的情况下，和其他应用程序进行有限的交互，但这些交互是有限的，且建立在双方信任允许的前提下。

<!--more-->

### 沙盒目录

我们可以通过以下代码获取应用程序的沙盒目录，当然模拟器和真机的沙盒路径是不一样的。

	NSLog(@"%@", NSHomeDirectory());

打开沙盒目录，我们可以看到三个子文件夹Documents、Library和tmp，每个文件夹的作用是不同的，以下分别介绍这三个子文件夹。

![](/img/iOS基础-沙盒目录和应用程序包/沙盒目录和应用程序包01.png)

获取沙盒目录子文件夹路径可以使用`NSHomeDirectory`方法然后拼接`Documents/ Library / tmp`字符串的方式，也可以使用`NSSearchPathForDirectoriesInDomains`方法直接获取。

1. Documents目录

	可读写的文件夹，可以用来存放图片，视频和其他重要数据。通过iTunes备份，应用程序升级时，不会改变里面的内容。
	
	获取Documents文件夹的代码：
	
		NSString *documents = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
	
2. Library

	获取Library文件夹的代码：
	
		NSString *library = [NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES) lastObject];	

	打开文件夹，可以看到，Library文件夹下有两个子文件夹Preference和Cache。

	* Preference

		主要用来存放配置文件、NSUserDefault 、plist文件等，通过iTunes备份，应用升级时，不会改变里面的内容。
	
	* Cache 

		Cache多用来保存临时数据、缓存文件等，iTunes不会备份此文件夹下的内容，有有效期限制。

3. tmp 

	存放临时数据，iTunes不会备份此文件夹下的内容，当应用程序关闭时，文件就被清空了。
	
	获取tmp文件夹的代码：
		
		NSLog(@"%@",NSTemporaryDirectory());
		
### 应用程序包

沙盒目录是用来保存App运行过程中产生的数据的，除了沙盒目录以外，App还有一个应用程序包目录，该目录不可读写，主要用来保存我们编写的程序、资源、xib文件等，比如我们在编写程序时拖入Xcode的图片资源就存放在这个包目录里。
	
每次应用启动时，会对包里面的所有数据进行CRC校验，如果增加、删除、修改里面的内容，则CRC校验，就会识别，应用程序将无法启动。

通常我们想要获取App目录里的内容，都是使用NSBundle获取。

1. 使用NSBundle加载图片

		NSBundle *bundle = [NSBundle mainBundle];
		NSString *imageNamed = [bundle pathForResource:@"麦子" ofType:@"jpg"];
		//通过文件全路径加载图片，无缓存
		//imageNamed方法有缓存
		UIImage *image = [UIImage imageWithContentsOfFile:imageNamed];
	
2. 使用NSBundle加载xib文件

		NSArray* nibViews =  [[NSBundle mainBundle] loadNibNamed:@"CenterView" owner:nil options:nil];
		CenterView *cv =[nibViews objectAtIndex:0];
