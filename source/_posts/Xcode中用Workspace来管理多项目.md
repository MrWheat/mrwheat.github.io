title: "Xcode中用Workspace来管理多项目"

date: 2016-05-26 15:25:51

description:

categories: Xcode配置

tags: [项目,开发,Xcode,iOS]

---

项目开发过程中经常会有某些特定的需求或者常用的功能，需要我们单独去封装一些模块去实现。最简单的方式当然是直接在工程中新建工具类来实现，对于一些小模块小功能，我们可以这么做。但是对于一个相对大一点的功能模块，为了代码的简洁、性能、可读性、复用性和耦合性等等等，推荐把它们放到一个单独的工程里，通过静态库的方式，应用到任何其他需要的项目里，就像是用其他第三方SDK那样。

这样做的好处有很多，封装好的代码我们其他项目里照样可以使用，只要把我们封装好的静态库拖进去就可以了。不过问题也是存在的，首先，新建静态库本身就是一件很繁琐的事情，虽然程序员不怕折腾，但有时候这种繁琐并不能一劳永逸，反而成为累赘。当功能需要不断更新时，我们不可能在两个项目里跳来跳去。

对于需要不断更新的模块，可以使用Workspace来进行管理。其实本质上，Workspace也是编译静态库然后给主工程使用，但不用先打开工程A，编译出libA.framework，然后把文件拖到工程B，然后再在工程B里面使用。

废话说了这么多，下面进入正题。

<!--more-->

#####新建项目工程

首先为你的项目新建一个工程，如果有了则不必新建。

![](http://7pumug.com1.z0.glb.clouddn.com/138D9CAC-CC1F-42BC-946D-1FE4388F8AD2.png)

#####新建静态库

根据需要新建静态库，静态库的创建这里就不赘述，不懂的请移步[framework静态库创建](http://www.hackmz.com/2016/05/25/iOS开发-静态库制作-三/)。

![](http://7pumug.com1.z0.glb.clouddn.com/5713DA18-D3E2-42FB-8BFB-AA453012EBC4.png)

#####构建Workspace

1. 打开Xcode，创建一个Workspace。

	![](http://7pumug.com1.z0.glb.clouddn.com/788D7DEB-AA2C-4115-8885-2E2BF218B43B.png)

2. 将Workspace和项目放到一起，这时候目录里应该有下面这三个文件。

	![](http://7pumug.com1.z0.glb.clouddn.com/25E14EC1-C359-414A-8B5D-227D44EE037D.png)

3. 然后打开Workspace，将项目添加进去，就是将项目的.xcodeproj文件添加进去。

	![](http://7pumug.com1.z0.glb.clouddn.com/4A672F33-A6F2-4B3A-A0A8-C826DBACAFE7.png)

#####指定引用库的头文件路径

在主项目的`Build Settings`里找到`Header Search Paths`，添加一项`$(SRCROOT)/../MZHelpeCenter`，并且设置为`recursive`，否则找不到头文件。

![](http://7pumug.com1.z0.glb.clouddn.com/38A103D3-9BC1-4DC7-90B2-8F653C91C178.png)

#####把静态库添加到主工程里

到主工程的`Build Phases`的`Link Binary With Libraries`里添加，点击"+"按钮，会给出整个Workspace可选的静态库，把我们创建的静态库`MZHelpeCenter.framework`加入进来。

![](http://7pumug.com1.z0.glb.clouddn.com/593EB492-AA97-48C3-992B-8E9EE8D4D4BB.png)

#####使用方法

使用方式和静态库的使用方式相同，直接包含头文件即可。

![](http://7pumug.com1.z0.glb.clouddn.com/53FE944A-173D-47F7-8DDC-84BCC3D54FE2.png)





