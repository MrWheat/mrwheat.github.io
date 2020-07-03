title: "用CocoaPods管理你的第三方库"

date: 2016-06-27 14:30:16

description:

categories: Xcode配置

tags: [管理,开发,工具,iOS]

---

在开发一个iOS应用时，不可避免的会使用很多的第三方库，而一个一个的手动下载和添加是很麻烦的，通过CocoaPods则可以让我们很方便的下载和管理这些第三方依赖库。好吧，又一个懒人科技。

<!--more-->

### CocoaPods安装

CocoaPods是依赖于Ruby实现的，因此在使用CocoaPods时，我们还需要事先搭建好Ruby环境，Mac OS本身自带Ruby，为了确保Ruby是最新的，还是更新一下比较好。

	$ruby -v	// 查看ruby版本的命令 
	$sudo gem update —system	// 更新ruby，需要管理员权限
	
安装CocoaPods需要访问cocoapods.org，因为墙的存在，可能导致速度很慢，因此在安装之前先更换下载源。

	$gem sources --remove https://rubygems.org/	// 移除现有 Ruby 默认源
	$gem sources -a https://ruby.taobao.org/	// 使用新的源
	$gem sources -l	// 验证新源是否替换成功
	
提示如下则代表替换成功。
	
	*** CURRENT SOURCES ***
	http://ruby.taobao.org/
	
安装CocoaPods，耐心等待。

	$sudo gem install cocoapods
	$pod setup
	
### CocoaPods使用方法

搜索需要的类库，使用如下命令，将会输出搜索到的所有类库版本和信息。

	$pod search 类库名
	
使用cd命令到工程目录下，并创建Podfile文件

	$touch Podfile
	
打开并编辑Podfile文件
	
	$vim Podfile
	
下面提供了几个编辑Podfile文件的模板

1. 单个target中使用Pods依赖库

		platform :ios, '8.0'	// 所有库支持的IOS最低版本
		target 'MyApp' do		// 'MyApp'是项目工程的target
			pod 'AFNetworking', '~> 2.6'
			pod 'ORStackView', '~> 3.0'
			pod 'SwiftyJSON', '~> 2.3'
		end

2. 多个target中使用相同的Pods依赖库		
		
		link_with ‘TargetOne’, ‘TargetTwo’	//使用link_with关键字来实现
		platform :ios
			pod 'Reachability', '~> 3.0.0'
			pod 'SBJson', '~> 4.0.0'
		platform :ios, '7.0'
			pod 'AFNetworking', '~> 2.0'
		
3. 不同的target使用完全不同的Pods依赖库

		target :'TargetOne' do
		platform :ios
			pod 'Reachability', '~> 3.0.0'
			pod 'SBJson', '~> 4.0.0'
		platform :ios, '7.0'
			pod 'AFNetworking', '~> 2.0'
		end
		target :'TargetTwo' do
			pod 'OpenUDID', '~> 1.0.0'
		end

将第三方库导入工程

	$pod install	// 每次更改Podfile文件都需要重新执行
	
最后通过workspace文件打开工程。
	
### CocoaPods常用命令

* pod install
	
	根据Podfile文件指定的内容，安装依赖库，如果有Podfile.lock文件而且对应的Podfile文件未被修改，则会根据Podfile.lock文件指定的版本安装。
	
	每次更新了Podfile文件时，都需要重新执行该命令，以便重新安装Pods依赖库。
	
* pod update

	若果Podfile中指定的依赖库版本不是写死的，当对应的依赖库有了更新，无论有没有Podfile.lock文件都会去获取Podfile文件描述的允许获取到的最新依赖库版本。也可以使用`pod update 库名`的方式对某个依赖库进行单独更新。
	
* pod search 类库名

	搜索得到类库的版本信息，然后编写Podfile文件，具体写法和表示含义有：
	
		pod ‘AFNetworking’      //不显式指定依赖库版本，表示每次都获取最新版本
		pod ‘AFNetworking’, ‘2.0’		//只使用2.0版本
		pod ‘AFNetworking’, ‘> 2.0’    	//使用高于2.0的版本
		pod ‘AFNetworking’, ‘>= 2.0’     //使用大于或等于2.0的版本
		pod ‘AFNetworking’, ‘< 2.0’    	//使用小于2.0的版本
		pod ‘AFNetworking’, ‘<= 2.0’     //使用小于或等于2.0的版本
		pod ‘AFNetworking’, ‘~> 0.1.2’   //使用大于等于0.1.2但小于0.2的版本
		pod ‘AFNetworking’, ‘~>0.1’     //使用大于等于0.1但小于1.0的版本
		pod ‘AFNetworking’, ‘~>0’     	//高于0的版本，写这个限制和什么都不写是一个效果，都表示使用最新版本

* pod setup

	用于跟新本地电脑上的保存的Pods依赖库tree。由于每天有很多人会创建或者更新Pods依赖库，这条命令执行的时候会相当慢，还请耐心等待。我们需要经常执行这条命令，否则有新的Pods依赖库的时候，执行pod search命令是搜不出来的。

### 错误汇总

* https协议

	**错误描述：**Error fetching http://ruby.taobao.org/:bad response Not Found 404 (http://ruby.taobao.org/specs.4.8.gz)

	**解决方案：**把安装流程中`gem sources -a http://ruby.taobao.org/`改为：`gem sources -a https://ruby.taobao.org/`
	
* 苹果OS X EL Capitan系统问题

	**错误描述：**ERROR:  While executing gem ... (Errno::EPERM)Operation not permitted - /usr/bin/pod

	**解决方案：**苹果系统升级OS X EL Capitan后会出现的插件错误，将安装流程`sudo gem install cocoapods`改为`sudo gem install -n /usr/local/bin cocoapods`

* 添加平台版本号

	**错误描述：**[!] Unable to satisfy the following requirements:- \`AFNetworking (~> 2.3.1)\` required by \`Podfile\` Specs satisfying the \`AFNetworking (~> 2.3.1)\` dependency were found, but they required a higher minimum deployment target.

	**解决方案：**Podfile文件中platform:ios,‘8.0’后边的8.0是平台版本号，一定要加上。

* 指明target

	**错误描述**：The dependency `` is not used in any concrete target

	**解决方案：** Podfile升级之后到最新版本，pod里的内容必须明确指出所用第三方库的target。

		$pod --version	//查看pod版本
		$sudo gem install cocoapods --pre	// pod升级
		
	Podfile文件使用如下格式：
	
		platform :ios, '8.0'
		target 'MyApp' do	// 'MyApp'是项目工程的target
  			pod 'AFNetworking', '~> 2.6'
  			pod 'ORStackView', '~> 3.0'
  			pod 'SwiftyJSON', '~> 2.3'
		end
		
	或者
	
		platform :ios, '8.0'
		def pods
 	 		pod 'AFNetworking', '~> 2.6'
  			pod 'ORStackView', '~> 3.0'
  			pod 'SwiftyJSON', '~> 2.3'
		end
		target 'MyApp' do
  			pods
		end
		
### 参考

[RubyGems 镜像](https://ruby.taobao.org)

[cocoapods使用指南](http://www.jianshu.com/p/7884ec8da77e)

[CocoaPods安装使用及配置私有库](http://www.exiatian.com/cocoapods安装使用及配置私有库/)






