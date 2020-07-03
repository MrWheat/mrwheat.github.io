title: "OC中使用宏定义进行调试"

date: 2016-06-02 18:01:46

description: 

categories: 语法基础

tags: [iOS,开发,宏定义]

---

想了想还是把OC中经常用到的宏定义语法单独列了一章出来，毕竟对于不同的语言，不同的平台，使用起来还是千差万别的，如果对`#define`还不熟悉的朋友可以参看以前写的一篇博客[#define用法总结](http://www.hackmz.com/2015/05/21/define用法总结/)。

<!--more-->
	
### ...的用法

代码调试，用的最多的方法就是打印数据，对于输出方法来说，参数一般都是可变的，在调试程序时，你可能希望自己定义参数可变的输出函数，那么可变参数宏会是一个选择。

	#define FMWLog(id, ...) NSLog((@"%s [Line %d] " id),__PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
	
其中...表示可变的参数列表，##__VA_ARGS__在预处理中为实际的参数集。

### 宏定义过滤NSLog

应用开发过程中，不可避免的会大量使用NSLog打印数据，但是在项目上线或是release的时候为了提高应用的执行效率，我们需要去掉这些打印方法。此时你辛辛苦苦写了那么多的NSLog，我们不可能手动去注释，有些做法是设一个开关变量，企图用这个总开关开启或注释NSLog。但更方便的则是利用宏定义进行设置。

* 对于系统的NSLog方法

		#ifndef __OPTIMIZE__
		#define NSLog(...) NSLog(__VA_ARGS__)
		#else
		#define NSLog(...) {}
		#endif

* 如果是自定义输出方法
	
		#if DEBUG	// 也可用#ifndef __OPTIMIZE__来代替
		    #define FMWLog(id, ...) NSLog((@"%s [Line %d] " id),__PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
		#else
		    #define FMWLog(id, ...)
		#endif
	
这样设置后，只需要在Product ->Scheme->Edit Scheme ->info选择，是release ,还是debug版本即可。如果是debug版本，则打印数据，如果是release版本则不打印。

我们发布到appstore上的版本均是release版本，这里简单说下这两个版本的差异。release是发行版本，比debug版本要小一些，他们调用两个不同底层库，debug包含的信息多，可以断点调试，单步执行，使用TRACE/ASSERT等调试输出语句，但是release 版本不包含调试信息，运行速度比较快。

另外在此处设置的DEBUG参数可以在下面的路径进行设置：`工程->Target->Build Setting ->Preprocessor Macros`。默认系统已经给出了DEBUG的参数。如果要增加新的参数，则在那里进行增加。

### \_\_OBJC\_\_宏定义

我们经常会见到在.pch文件中加上`#ifdef __OBJC__`这句宏定义，它的作用是什么呢？

因为在一个OC工程中，可能包含.m、.mm、.c、.cpp四类编译文件，这四类文件均会引用.pch预编译头。在编译.c、.cpp时，因为语法不兼容OC，所以预编译头中不能包含objc代码。

而.pch是多类源文件共用的，所以在pch中，oc头文件要用\_\_OBJC\_\_包含起来，表示宏内引用的文件确保只被使用Objective-C语言的文件所引用，保证引用关系的清晰。

	#ifdef __OBJC__
	#import <UIKit/UIKit.h>
	#import "AcctConst.h"
	#import "AcctHeader.h"
	#import "AcctMacro.h"
	#import "EMSDK.h"
	#endif

### 宏定义指令

	#define 定义一个预处理宏
	#undef 取消宏的定义
	#include 包含文件命令
	#include_next 与#include相似, 但它有着特殊的用途
	#if 编译预处理中的条件命令, 相当于C语法中的if语句
	#ifdef 判断某个宏是否被定义, 若已定义, 执行随后的语句
	#ifndef 与#ifdef相反, 判断某个宏是否未被定义
	#elif 若#if, #ifdef, #ifndef或前面的#elif条件不满足, 则执行#elif之后的语句, 相当于C语法中的else-if
	#else 与#if, #ifdef, #ifndef对应, 若这些条件不满足, 则执行#else之后的语句, 相当于C语法中的else
	#endif #if, #ifdef, #ifndef这些条件命令的结束标志.
	#defined 与#if, #elif配合使用, 判断某个宏是否被定义
	#line 标志该语句所在的行号
	# 将宏参数替代为以参数值为内容的字符窜常量
	## 将两个相邻的标记(token)连接为一个单独的标记
	#pragma 说明编译器信息
	#warning 显示编译警告信息
	#error 显示编译错误信息
	
### 一些系统宏定义

	##__VA_ARGS__	// __VA_ARGS__ 是一个可变参数的宏。宏前面加 上##的作用在于，当可变参数的个数为0时，这里的##起到把前面多余的","去掉的作用,否则会编译出错。
	
	__FILE__	// 在预编译时会替换成当前的源文件名
	
	__LINE__	// 在预编译时会替换成当前的行号
	
	__FUNCTION__	// 在预编译时会替换成当前的函数名称
	
### 开发常见的宏定义

	//AppDelegate
	#define APPDELEGATE [(AppDelegate*)[UIApplication sharedApplication]  delegate]
	//----------------------系统设备相关----------------------------
	//获取设备屏幕尺寸
	#define SCREEN_WIDTH ([UIScreen mainScreen].bounds.size.width)
	#define SCREEN_HEIGHT ([UIScreen mainScreen].bounds.size.height)//应用尺寸
	#define APP_WIDTH [[UIScreen mainScreen]applicationFrame].size.width
	#define APP_HEIGHT [[UIScreen mainScreen]applicationFrame].size.height
	//获取系统版本
	#define IOS_VERSION [[[UIDevice currentDevice] systemVersion] floatValue]
	#define CurrentSystemVersion [[UIDevice currentDevice] systemVersion]
	#define isIOS4 ([[[UIDevice currentDevice] systemVersion] intValue]==4)
	#define isIOS5 ([[[UIDevice currentDevice] systemVersion] intValue]==5)
	#define isIOS6 ([[[UIDevice currentDevice] systemVersion] intValue]==6)
	#define isAfterIOS4 ([[[UIDevice currentDevice] systemVersion] intValue]>4)
	#define isAfterIOS5 ([[[UIDevice currentDevice] systemVersion] intValue]>5)
	#define isAfterIOS6 ([[[UIDevice currentDevice] systemVersion] intValue]>6)
	//获取当前语言
	#define CurrentLanguage ([[NSLocale preferredLanguages] objectAtIndex:0])
	
	//判断是否 Phone 4/5/6 是否是iPad
	#define Phone4 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 960), [[UIScreen mainScreen] currentMode].size) : NO)
	#define Phone5 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 1136), [[UIScreen mainScreen] currentMode].size) : NO)
	#define Phone6 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(750, 1334), [[UIScreen mainScreen] currentMode].size) : NO)
	#define Phone6Plus ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(1242,2208), [[UIScreen mainScreen] currentMode].size) : NO)
	#define isPad (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)
	//判断是真机还是模拟器
	#if TARGET_OS_IPHONE
	//iPhone Device
	#endif
	#if TARGET_IPHONE_SIMULATOR
	//iPhone Simulator
	#endif
	//----------------------系统设备相关----------------------------
	
	//----------------------内存相关----------------------------
	//使用ARC和不使用ARC
	#if __has_feature(objc_arc)
	//compiling with ARC
	#else
	// compiling without ARC
	#endif
	//释放一个对象
	#define SAFE_DELETE(P) if(P) { [P release], P = nil; }
	#define SAFE_RELEASE(x) [x release];x=nil
	//----------------------内存相关----------------------------
	
	//----------------------图片相关----------------------------
	//读取本地图片
	#define LOADIMAGE(file,ext) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle]pathForResource:file ofType:ext]]
	//定义UIImage对象
	#define IMAGE(A) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle] pathForResource:A ofType:nil]]
	//定义UIImage对象
	#define ImageNamed(_pointer) [UIImage imageNamed:_pointer]
	//可拉伸的图片
	#define ResizableImage(name,top,left,bottom,right) [[UIImage imageNamed:name] resizableImageWithCapInsets:UIEdgeInsetsMake(top,left,bottom,right)]
	#define ResizableImageWithMode(name,top,left,bottom,right,mode) [[UIImage imageNamed:name] resizableImageWithCapInsets:UIEdgeInsetsMake(top,left,bottom,right) resizingMode:mode]
	//建议使用前两种宏定义,性能高于后者
	//----------------------图片相关----------------------------
	
	//----------------------颜色相关---------------------------
	// rgb颜色转换（16进制->10进制）
	#define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]
	// 获取RGB颜色
	#define RGBA(r,g,b,a) [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:a]
	#define RGB(r,g,b) RGBA(r,g,b,1.0f)
	//背景色
	#define BACKGROUND_COLOR [UIColor colorWithRed:242.0/255.0 green:236.0/255.0 blue:231.0/255.0 alpha:1.0]
	//清除背景色
	#define CLEARCOLOR [UIColor clearColor]
	//----------------------颜色相关--------------------------
	
	//----------------------其他----------------------------
	//方正黑体简体字体定义
	#define FONT(F) [UIFont fontWithName:@"FZHTJW--GB1-0" size:F]
	//file
	//读取文件的文本内容,默认编码为UTF-8
	#define FileString(name,ext)            [[NSString alloc] initWithContentsOfFile:[[NSBundle mainBundle] pathForResource:(name) ofType:(ext)] encoding:NSUTF8StringEncoding error:nil]
	#define FileDictionary(name,ext)        [[NSDictionary alloc] initWithContentsOfFile:[[NSBundle mainBundle] pathForResource:(name) ofType:(ext)]]
	#define FileArray(name,ext)             [[NSArray alloc] initWithContentsOfFile:[[NSBundle mainBundle] pathForResource:(name) ofType:(ext)]]
	//G－C－D
	#define BACK(block) dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), block)
	#define MAIN(block) dispatch_async(dispatch_get_main_queue(),block)
	//Alert
	#define ALERT(msg) [[[UIAlertView alloc] initWithTitle:nil message:msg delegate:nil cancelButtonTitle:@"ok" otherButtonTitles:nil] show]
	
	//由角度获取弧度 有弧度获取角度
	#define degreesToRadian(x) (M_PI * (x) / 180.0)
	#define radianToDegrees(radian) (radian*180.0)/(M_PI)
	//----------------------其他-------------------------------
	
	//----------------------视图相关----------------------------
	//设置需要粘贴的文字或图片
	#define PasteString(string)   [[UIPasteboard generalPasteboard] setString:string];
	#define PasteImage(image)     [[UIPasteboard generalPasteboard] setImage:image];
	
	//得到视图的left top的X,Y坐标点
	#define VIEW_TX(view) (view.frame.origin.x)
	#define VIEW_TY(view) (view.frame.origin.y)
	
	//得到视图的right bottom的X,Y坐标点
	#define VIEW_BX(view) (view.frame.origin.x + view.frame.size.width)
	#define VIEW_BY(view) (view.frame.origin.y + view.frame.size.height )
	
	//得到视图的尺寸:宽度、高度
	#define VIEW_W(view)  (view.frame.size.width)
	#define VIEW_H(view)  (view.frame.size.height)
	//得到frame的X,Y坐标点
	#define FRAME_TX(frame)  (frame.origin.x)
	#define FRAME_TY(frame)  (frame.origin.y)
	//得到frame的宽度、高度
	#define FRAME_W(frame)  (frame.size.width)
	#define FRAME_H(frame)  (frame.size.height)
	//----------------------视图相关----------------------------
	
	//---------------------打印日志--------------------------
	//Debug模式下打印日志,当前行,函数名
	#if DEBUG
	#define DLog(FORMAT, ...) fprintf(stderr,"\nfunction:%s line:%d content:%s\n", __FUNCTION__, __LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);
	#else
	#define NSLog(FORMAT, ...) nil
	#endif
	//Debug模式下打印日志,当前行,函数名 并弹出一个警告
	#ifdef DEBUG
	#   define  WDLog(fmt, ...)  { UIAlertView *alert = [[UIAlertView alloc] initWithTitle:[NSString stringWithFormat:@"%s\n [Line %d] ", __PRETTY_FUNCTION__, __LINE__] message:[NSString stringWithFormat:fmt, ##__VA_ARGS__]  delegate:nil cancelButtonTitle:@"Ok" otherButtonTitles:nil]; [alert show]; }
	#else
	#   define NSLog(...)
	#endif
	//打印Frame
	#define LogFrame(frame) NSLog(@"frame[X=%.1f,Y=%.1f,W=%.1f,H=%.1f",frame.origin.x,frame.origin.y,frame.size.width,frame.size.height)
	//打印Point
	#define LogPoint(point) NSLog(@"Point[X=%.1f,Y=%.1f]",point.x,point.y)
	//---------------------打印日志--------------------------
	#endif
	
	
