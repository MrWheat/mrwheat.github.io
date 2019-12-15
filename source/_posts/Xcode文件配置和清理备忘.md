title: "Xcode文件配置和清理备忘"

date: 2016-05-21 16:58:12

description: 

categories: Xcode配置

tags: [Xcode,配置,备忘]

---

这是一篇关于Xcode配置和清理相关路径的备忘录。我们可以直接进入下面的路径中，进行添加和删除操作，手残党误试。

<!--more-->

###文件配置相关

* Xcode配置包文件

	有时候我们将手机设备升级了，Xcode没有同步更新，真机测试会遇到could not find developer disk image错误，这时候需要我们下载最新的包导入Xcode。

	在Finder状态下前往文件夹，快捷键：shift+command+G，填写路径`/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport` 

	![](http://7pumug.com1.z0.glb.clouddn.com/%E9%85%8D%E7%BD%AEXcode%E5%8C%85.png)

###文件清理相关

* 删除Xcode中多余的证书provisioning profile

	前往文件夹：`~/Library/MobileDevice/Provisioning Profiles`

* 删除Docsets

	前往文件夹：`~/Library/Developer/Shared/Documentation/DocSets`
	
* 删除DerivedData
    
    这是删的最爽的一个文件夹，时间久了不清理，分分钟清理出来十几个G。

	前往文件夹：`~/Library/Developer/Xcode/DerivedData`
	
* 删除Archives（建议备份dSYM文件）

	前往文件夹：`~/Library/Developer/Xcode/Archives`
	
* 删除模拟器中安装的Apps

    以iOS Simulator 6.1为例。

	前往文件夹：`~/Library/Application Support/iPhone Simulator/6.1/Applications`
	
* 删除对旧设备的支持

	前往文件夹：`~/Library/Developer/Xcode/iOS DeviceSupport`
	
* 删除模拟器的临时文件

    以iOS Simulator 6.1为例。

	前往文件夹：`~/Library/Application Support/iPhone Simulator/6.1/tmp`
	
* 删除旧版本的模拟器支持

	前往文件夹：`~/Library/Application Support/iPhone Simulator`
	
* 删除打包时生成的多余的产品

	前往文件夹：`~/Library/Developer/Xcode/Products`