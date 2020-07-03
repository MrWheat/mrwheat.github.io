title: "UIImage图片显示处理"

date: 2016-08-05 14:31:26

description:

categories: iOS开发 - 基础

tags: [图片,UIImage,iOS]

---

上回写过一篇关于图片旋转的问题，详情请见[iPhone图片上传服务器旋转的问题](http://www.hackmz.com/2016/05/30/iPhone图片上传服务器旋转的问题/)。

这只是图片使用过程中的一个小坑，类似的坑还有千万，我们不需要所有的坑都走一遍，也不用对每个坑做细致的调查，但对于常见的坑，掌握如何填平还是有必要的。

UIImageView作为最常用的控件之一，为了迎合需求，总是要求我们对图片做各种各样的处理，最常见的有拉伸，缩放，模糊，裁剪几种。

在这里分享图片处理的一些实用方法。

<!--more-->

### 加载图片

这个不必多说，有两种方式：

    UIImageView *imageV = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 300, 150)];
    
    // 第一种方式，通过文件路径加载，无缓存
    NSBundle *bundle = [NSBundle mainBundle];
    NSString *imageNamed = [bundle pathForResource:@"testImage" ofType:@"jpg"];
    imageV.image = [UIImage imageWithContentsOfFile:imageNamed];
    
    // 第二种方式，imageNamed:方法加载，有缓存
    imageV.image = [UIImage imageNamed:@"testImage.jpg"];
	
### 渲染模式

图片在显示的时候，我们可以根据当前视图的Tint Color对图片进行渲染。系统提供了一个枚举值(UIImageRenderingMode)来方便我们设置。

	UIImageRenderingModeAutomatic	// 根据图片使用环境自动渲染
	UIImageRenderingModeAlwaysOriginal	// 始终绘制图片原始状态，不使用Tint Color
	UIImageRenderingModeAlwaysTemplate	// 始终根据Tint Color绘制图片，忽略图片的颜色信息
    
使用方法：

    UIImage *image = [[UIImage imageNamed:@"testImage.jpg"] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];  
    
### 显示方式

系统提供了UIViewContentMode这个枚举值来设置图片的显示模式，下面介绍常用的几种。

1. UIViewContentModeScaleToFill	

	默认方式，拉伸显示，充满控件，显示整个图片

		imageV.contentMode = UIViewContentModeScaleToFill;

	![](/img/iOS基础-UIImage图片显示处理/UIImage图片显示处理01.png)

2. UIViewContentModeScaleAspectFit

	原图缩放显示，适应控件，直到两个宽高都小于或等于控件，但有部分空白
	
		imageV.contentMode = UIViewContentModeScaleAspectFit;

	![](/img/iOS基础-UIImage图片显示处理/UIImage图片显示处理02.png)

3. UIViewContentModeScaleAspectFill

	原图缩放直到某个宽或高和控件相等，但是有部分会超出控件，这是使用最多的一个样式。
	
		imageV.contentMode = UIViewContentModeScaleAspectFill;

	![](/img/iOS基础-UIImage图片显示处理/UIImage图片显示处理03.png)

	超出部分可以使用`imageV.layer.masksToBounds = YES;`进行裁剪。

	![](/img/iOS基础-UIImage图片显示处理/UIImage图片显示处理04.png)

4. UIViewContentModeLeft

	原图显示，图片靠左对齐，有部分会超出控件，当然类似的还有上下和右，这里就不一一介绍了。
	
		imageV.contentMode = UIViewContentModeLeft;

	![](/img/iOS基础-UIImage图片显示处理/UIImage图片显示处理05.png)

### 颜色转图片

对于想要找一个纯色图片作背景的人，这个方法再适合不过了，也不用麻烦美工妹子了，自己动手丰衣足食。

	- (UIImage *)imageWithColor:(UIColor *)color {
	    CGRect rect = CGRectMake(0.0f, 0.0f, 1.0f, 1.0f);
	    UIGraphicsBeginImageContext(rect.size);
	    CGContextRef context = UIGraphicsGetCurrentContext();
	    CGContextSetFillColorWithColor(context, [color CGColor]);
	    CGContextFillRect(context, rect);
	    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
	    UIGraphicsEndImageContext();
	    return image;
	}
	
### 图片转颜色

对于某些不支持图片只能使用颜色作背景的控件，比如UIView，如果想要加个背景怎么办呢，新建一个UIImageView也可以，不过下面这个好像更简单一些。

	UIColor *color = [UIColor colorWithPatternImage:[UIImage imageNamed:@"麦子.jpg"]];


### 图片圆角设置

设置图片圆角用处也很多，最常见的就是设置头像。

    imageV.layer.masksToBounds = YES;
    // 数值为正方形宽高的一半
    imageV.layer.cornerRadius = 150;
    // 为了防止头像变形
    imageV.contentMode = UIViewContentModeScaleAspectFill;
    
![](/img/iOS基础-UIImage图片显示处理/UIImage图片显示处理06.png)
    
