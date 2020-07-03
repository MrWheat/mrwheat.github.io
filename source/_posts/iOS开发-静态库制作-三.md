title: "iOS开发-静态库制作(三)"

date: 2016-05-25 11:26:59

description: 

categories: iOS笔记

tags: [iOS,开发,静态库]

---

XCode6以前因为不支持.framework自定义静态库的制作，使用最多的是.a静态库，但是.a静态库制作完成之后，我们还必须保存工程的.h文件，看起来有些不伦不类的，封装的不够彻底，而.framework则不需要，本篇主要介绍.framework静态库的制作和使用。

<!--more-->

### 制作步骤

* 首先新建一个项目，选择Framework&Library中的Cocoa Touch Framework。

	![](http://7pumug.com1.z0.glb.clouddn.com/%E5%88%9B%E5%BB%BAframework%E5%BA%93.png)
	
* 创建完成之后开始编写我们需要封装的代码，为了演示我新建了三个类。

	![](http://7pumug.com1.z0.glb.clouddn.com/92639EC5-C7F3-4811-9994-5B8D396A1CD5.png)
	
* 配置参数，这里制作的是静态库，需要将`Mach-O Type`设置为`Static Library`。
	
	![](http://7pumug.com1.z0.glb.clouddn.com/C1B1F1BD-E9F9-472B-AD3D-C46DA0900FE7.png)
	
* 和.a类似，生成的静态库需要进行合并才能适应不同的平台，为了方便，我们可以向Xcode添加脚本来解决这个繁琐的问题。

	1. 新建target
		
		![](http://7pumug.com1.z0.glb.clouddn.com/151005_aEul_2340880.png)
		
	2. 选择Aggregate

		![](http://7pumug.com1.z0.glb.clouddn.com/199660A8-EFED-4D46-B6FE-FD302BA12D36.png)
	
	3. 我们在target的Build Phases中点击加号

		![](http://7pumug.com1.z0.glb.clouddn.com/FB168E9C-2421-423F-B3E4-D5A568A0FA17.png)
	
	4. 添加一个Run Script

		![](http://7pumug.com1.z0.glb.clouddn.com/151553_6MXT_2340880.png)
	
	5. 在里面添加脚本，脚本代码如下：
	
		```objectivec
		# Sets the target folders and the final framework product.
		# 如果工程名称和Framework的Target名称不一样的话，要自定义FMKNAME
		# 例如: FMK_NAME = "MyFramework"
		FMK_NAME=${PROJECT_NAME}
		# Install dir will be the final output to the framework.
		# The following line create it in the root folder of the current project.
		INSTALL_DIR=${SRCROOT}/Products/${FMK_NAME}.framework
		# Working dir will be deleted after the framework creation.
		WRK_DIR=build
		DEVICE_DIR=${WRK_DIR}/Release-iphoneos/${FMK_NAME}.framework
		SIMULATOR_DIR=${WRK_DIR}/Release-iphonesimulator/${FMK_NAME}.framework
		# -configuration ${CONFIGURATION}
		# Clean and Building both architectures.
		xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphoneos clean build
		xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphonesimulator clean build
		# Cleaning the oldest.
		if [ -d "${INSTALL_DIR}" ]
		then
		rm -rf "${INSTALL_DIR}"
		fi
		mkdir -p "${INSTALL_DIR}"
		cp -R "${DEVICE_DIR}/" "${INSTALL_DIR}/"
		# Uses the Lipo Tool to merge both binary files (i386 + armv6/armv7) into one Universal final product.
		lipo -create "${DEVICE_DIR}/${FMK_NAME}" "${SIMULATOR_DIR}/${FMK_NAME}" -output "${INSTALL_DIR}/${FMK_NAME}"
		rm -r "${WRK_DIR}"
		open "${INSTALL_DIR}"
		```

		![](http://7pumug.com1.z0.glb.clouddn.com/957ECC68-67E2-4344-9581-714D29A56643.png)
		
		
* 代码编写完成以后，我们需要设置哪些文件是暴露给开发者的，哪些是需要隐藏的，如下图，我们将需要暴露的文件移到Public下即可，Project中的文件是隐藏的，Private中的文件跟Public一样也是暴露的，博主暂时也不清楚Private文件的用意。

	![](http://7pumug.com1.z0.glb.clouddn.com/4DF62E2A-C92E-4959-86DA-5B86B66E0F5B.png)

### 使用方法	

使用方式如下图，直接将.framework拖入工程即可。

![](http://7pumug.com1.z0.glb.clouddn.com/F770E5E4-933D-479C-9EFC-FDFC95B9DC05.png)

### 注意事项

1. Category错误

	错误描述：静态库中如果包含了Category（分类），有时候在使用静态库的工程中会报“方法找不到”的错误（unrecognized selector sent to instance）。

	具体原因：参见[编译参数-objc说明](http://www.tuicool.com/articles/qQzeia)

	解决方案：在使用静态库的工程中配置`Other Linker Flags`为-ObjC
	
2. 制作成动态库

	错误描述：在使用静态库时，运行报错(Reason: Image Not Found)

	具体原因：可能由于没有设置`Mach-O Type`，做的是动态库，在使用的时候需要额外加一个步骤，要把Framework同时添加到`General --> Embedded Binaries`中。

	解决方案：将`Mach-O Type`设置为`Static Library`
	
### 参考
	
[Xcode6制作动态及静态Framework](http://www.cocoachina.com/ios/20141126/10322.html)

[iOS中制作可复用的框架Framework](http://my.oschina.net/u/2340880/blog/491268?fromerr=qgXLWLyI)

[iOS开发——创建你自己的Framework](http://www.cocoachina.com/ios/20150127/11022.html)
