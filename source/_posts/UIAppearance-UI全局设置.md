title: "UIAppearance UI全局设置"

date: 2016-07-04 10:57:19

description:

categories: iOS笔记

tags: [笔记,系统类,iOS]

---

在开发中，如果想要对一些控件类，比如UIImageView、UILabel、UINavigationBar等，做一些全局的改变，通常的做法都是通过继承的方式重写该类，这样新建一个一个文件，做的多了，会感觉很麻烦。在iOS5以后，苹果提供了更简单的方式，通过UIAppearance，你可以很轻松的管理你的界面。UIAppearance提供了两个方法：

* \+ (id)appearance
* \+ (id)appearanceWhenContainedIn:(Class <>)ContainerClass,...

<!--more-->

###appearance使用

appearance方法使用简单，比如要定制应用中所有UIBarButtonItem的颜色，可以在UIBarButtonItem的外观代理中设置tintColor。

	[[UINavigationBar appearance] setBarTintColor:[UIColor grayColor]];

或者你也可以这样写：

	UINavigationBar *appearance = [UINavigationBar appearance];
	[appearance setBarTintColor:[UIColor grayColor]];

###appearanceWhenContainedIn使用

这个方法可以用来设置某个类在其他类里的改变。

比如我想设置UIBarButtonItem在UINavigationBar、UIPopoverController、UITabbar中的TintColor，而在其他地方则保持不变。

    [[UIBarButtonItem appearanceWhenContainedIn:[UINavigationBar class], [UIPopoverController class],[UITabBar class], nil] setTintColor:[UIColor clearColor]];
    
###自定义类实现UIAppearance

如果想要在我们自定义的类中实现UIAppearance，需要在的类中实现UIAppearanceContainer协议，同时，在Objective-C中，还需要将相关的方法用UI_APPEARANCE_SELECTOR来标记，在Swift中，则需要在对应的属性或方法前面加上dynamic。

但实际上，UIView已经实现了UIAppearance和UIAppearanceContainer协议。因此，我们在其子类中不再需要显式地去声明实现这两个接口。

比如自定义Cell时，通过UIAppearance设置图片的圆角。

	// DateCell.h
    #import <UIKit/UIKit.h>
	@interface DateCell : UITableViewCell
	@property (nonatomic, strong) UIImageView *imageV;
	@property (nonatomic, assign) CGFloat radios UI_APPEARANCE_SELECTOR;
	@end
	
	// DateCell.m
	- (void)setRadios:(CGFloat)radios {
	    _radios = radios;
	    self.imageV.layer.cornerRadius = radios;
	    self.imageV.layer.masksToBounds = YES;
	}
	
	// 在控制器的viewDidLoad方法里
	[DateCell appearance].radios = 20;
    
###注意事项

* 使用UIAppearance设置UI效果最好采用全局的设置，在所有界面初始化前开始设置。对于在使用UIAppearance之前已经添加到界面上的控件，则没有效果。

* 对于自定义的UI控件，使用UIAppearance无法直接改变UI控件的子控件。

		// 自定义了一个Cell，在Cell中有一个UIImageView。
		#import <UIKit/UIKit.h>
		@interface DateCell : UITableViewCell
		@property (nonatomic, strong) UIImageView *imageV;
		@end
		
		// 在控制器的viewDidLoad方法里，直接设置imageV的圆角。
		[DateCell appearance].imageV.layer.cornerRadius = 20;
    	[DateCell appearance].imageV.layer.masksToBounds = YES;
    
    这种方法并不能达到预期的效果，因此我们需要给cell添加一个额外的属性，重写该属性的set方法，间接更改imageV的圆角。
    
    	// DateCell.h
	    #import <UIKit/UIKit.h>
		@interface DateCell : UITableViewCell
		@property (nonatomic, strong) UIImageView *imageV;
		@property (nonatomic, assign) CGFloat radios UI_APPEARANCE_SELECTOR;
		@end
		
		// DateCell.m
		- (void)setRadios:(CGFloat)radios {
		    _radios = radios;
		    self.imageV.layer.cornerRadius = radios;
		    self.imageV.layer.masksToBounds = YES;
		}
		
		// 在控制器的viewDidLoad方法里
		[DateCell appearance].radios = 20;
		
###参考

[UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/index.html#//apple_ref/occ/intfcm/UIAppearance/appearanceForTraitCollection:)

[自定义系统控件的外观：UIApearance](http://www.cocoachina.com/ios/20150723/12671.html)