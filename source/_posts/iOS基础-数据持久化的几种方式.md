title: "iOS数据持久化的几种方式"

date: 2016-07-26 10:13:11

description:

categories: iOS开发 - 基础

tags: [iOS,数据,存储]

---

数据持久化又称数据序列化，指的是一种将数据长久的保存起来的方式。

一般我们保存数据的方式都是将数据保存在本地磁盘上或者是服务器上。对比内存中的数据来说，更安全，也不易丢失。

以下列举了几种常见的保存数据的方式，每种方式各有优缺点，使用的时候我们可以根据数据的大小类型格式要求等综合考虑。

<!--more-->

### 本地数据存储

本地数据存储都是存储在沙盒目录的，关于沙盒目录的介绍，请看[沙盒目录和应用程序包](http://www.hackmz.com/2016/07/25/沙盒目录和应用程序包/)。

优点：加载速度快、节省流量、不需要网络就能获取、不需要服务器、操作简单。

缺点：占用内存、数据无法共享、App卸载之后数据就消失了。

对于iOS应用来讲，本地数据存储的方式大致有以下几种：

#### 普通文件方式(文本文件)

一般存储比较大型的数据时，我们采用这种方式，具体的操作原理请看[iOS开发本地缓存管理](http://www.hackmz.com/2016/07/22/iOS开发本地缓存管理/)。

#### plist文件（属性列表）

对于plist文件来说，它只能使用NSArray或NSDictionary作为根(root)。同时保存的数据类型也比较有限，只能是NSArray、NSMutableArray、NSDictionary、NSMutableDictionary、NSData、NSMutableData、NSString、NSMutableString、NSNumber和NSDate，无法保存自定义的对象。
	
使用方法：

	// 1.获取存储路径
	NSString *doc = NSHomeDirectory();
	doc = [doc stringByAppendingPathComponent:@"Documents"];
	NSString *file = [doc stringByAppendingPathComponent:@"user.plist"];
	// 2.读取数据，如果根是数组，则将plist文件读成数组，如果是字典，则读成字典
	NSMutableArray *infoArray = [NSMutableArray arrayWithContentsOfFile:file];
	// 3.判断数据是否存在	
	if (infoArray == nil) {
		infoArray = [NSMutableArray array];
	}
	// 4.往数组/字典里添加数据
	// ...........
	// 5.将数据写入Plist文件
	[infoArray writeToFile:file atomically:YES];

#### NSUserDefault (preference、偏好设置)

NSUserDefault是一个单例，它本身维护着一个plist文件，根是一个字典。

它是专门用来保存应用程序的配置信息的，一般不要在偏好设置中保存其他数据。

如果没有调用synchronize方法，系统会根据I/O情况不定时刻地保存到文件中。所以如果需要立即写入文件的就必须调用synchronize方法。

偏好设置会将所有数据保存到同一个文件中，即preference目录下的一个以此应用包名来命名的plist文件。

使用方法：

	// 1.获取userDefault单例对象
	NSUserDefaults *ud = [NSUserDefaults standardUserDefaults];
	// 2.获取单例对象里的值，因为是字典，因此使用objectForKey:获取单例里的值
	NSString *valueString = [ud objectForKey:@"demo"];
	NSLog(@"%@", valueString);
	// 3.写入数据到单例对象中
	[ud setObject:@"麦子" forKey:@"demo"];
	// 3.将单例同步到文件中(写文件)
	[ud synchronize];

#### 归档和解档

plist文件只能保存有限的数据格式，对自定义类的对象就无能为力了。而归档则可以归档我们自定义的类的对象。

要想实现归档和解档，首先需要遵守NSCoding协议，而对于系统大部分类来说都遵循了NSCoding协议，因此可以直接进行归档和解档操作。而对于自定义类来说，则需要我们自己遵守NSCoding协议，这里以自定义类进行举例。

使用方法：

1. 自定义类，要想进行归档，需要遵守NSCoding协议

		// 这里以MZDataModel这个类为例
		@interface MZDataModel : NSObject <NSCoding>
		@property (nonatomic, copy) NSString *name;
		@property (nonatomic, copy) NSString *password;
		@property (nonatomic, assign) NSInteger age;
		@end
	
2. 在自定义类里，实现encodeWithCoder:和initWithCoder:两个协议方法

		// 归档的时候，会自动调用这个方法
		- (void)encodeWithCoder:(NSCoder *)aCoder {
		    [aCoder encodeObject:self.name forKey:@"name1"];
		    [aCoder encodeObject:self.password forKey:@"password1"];
		    [aCoder encodeInteger:self.age forKey:@"age1"];
		}
		// 解档的时候，会自动调用这个方法
		- (id)initWithCoder:(NSCoder *)aDecoder {
		    // 这里，因为我们的父类是NSObject，如果父类也是自定义的，那么这里要调用
		    // [super initWithCoder:aDecoder];
		    if (self = [super init]) {
		        self.name = [aDecoder decodeObjectForKey:@"name1"];
		        self.password = [aDecoder decodeObjectForKey:@"password1"];
		        self.age = [aDecoder decodeIntegerForKey:@"age1"];
		    }
		    return self;
		}
	
3. 归档
		
		// 获取文件路径
		NSString *file = [NSHomeDirectory() stringByAppendingPathComponent:@"Documents"];
		file = [file stringByAppendingPathComponent:@"user.archiver"];
		MZDataModel *model = [[MZDataModel alloc] init];
		model.age = 18;
		model.name = @"麦子";
		model.password = @"888888";
		[NSKeyedArchiver archiveRootObject:model toFile:file];
      
4. 解档

		NSString *file = [NSHomeDirectory() stringByAppendingPathComponent:@"Documents"];
		file = [file stringByAppendingPathComponent:@"user.archiver"];
		MZDataModel *model = [NSKeyedUnarchiver unarchiveObjectWithFile:file];
		// 判断数据是否存在
		if (model) {
		    NSLog(@"%ld", model.age);
		    NSLog(@"%@", model.name);
		    NSLog(@"%@", model.password);
		}

#### 数据库

数据库类别多中多样，iOS常用的数据库存储方式主要sqlite、FMDB和core data等。以上存储方式只适合存储少量数据，因为每次增加或者修改某个文件里的数据，都必须将原文件整个取出，然后覆盖写入。而数据库则很好的解决了这一问题，它适合存储大量的数据，而且能够方便的对每条数据单独的进行增删改查的操作。关于数据库的操作以后有时间再写。

### 数据存储到服务器

这是最常用的数据存储方式，对于网络数据来说，目前比较流行的是AFNetworking库，感兴趣的可以去研究研究，关于网络数据方面的内容，以后会单独提及。

优点：不需要占用内存、数据能够共享、能够长久的保存数据。

缺点：网络慢时加载较慢、耗费流量、需要购买服务器、需要进行后台管理、操作复杂。
