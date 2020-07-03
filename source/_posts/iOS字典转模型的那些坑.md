title: "iOS字典转模型的那些坑"

date: 2016-08-11 17:00:14

description:

categories: iOS笔记

tags: [iOS,数据,模型]

---

无论是XML解析还是JSON解析，最终得到的数据通常我们都是以数据模型的形式保存在本地的，为什么不直接用字典呢？

首先大多数编译器都有自动提示补全功能，将字典的key转换成模型里的属性，就能很好的利用这一功能，提高开发速度，比复制粘贴还快哦。

其次，如果模型的属性写错了，编译的时候，编译器会提示报错，这样就能及时修正BUG，而字典的key，你写成啥样，编译器也不会管你的死活。

最后一点，不管怎么样，写成数据模型的形式，起码看起来也比字典专业不是么？

字典转模型的方式多种多样，网上的开源库也很多。但在实际开发过程中，稍有不慎，一些细节问题同样会让你头疼。

<!--more-->

### 字典键值对多于模型属性。

有些开发者会选择使用KVC的方式来进行字典模型转换。

使用setValuesForKeysWithDictionary:方法能够直接将字典的value赋值给模型，但是有时候会遇到程序莫名崩溃的情况。通过比对，发现字典的某些key，模型中并不存在对应的属性。
	
 当找不到对应属性时，程序首先会到模型里去寻找master的getter方法，看有没有`- (id)master;`这个方法，如果没有，则会寻找`_master`的成员变量，如果没有，则继续寻找`master`的成员变量，如果还没有，会看有没有实现`valueForUndefinedKey`方法，如果没有，程序就崩溃了。
 
 因此我们可以在模型里重写以下方法：

	- (void)setValue:(id)value forUndefinedKey:(NSString *)key {
	    // 内部并不需要写任何东西
	}
		
### 字典的key为OC关键字

我们不能要求每一个后台都了解OC，有时候遇到后台数据字段和OC关键字冲突的情况下，更改后台代码又特别麻烦。这种情况下，以description关键字为例，我们可以在模型的.m文件中增加这样一行代码：

	@synthesize description = _description;
	
当然，如果你使用MJExtension三方库，也可以这样写，然后在模型里定义一个des属性来代替description：

	+ (NSDictionary *)replacedKeyFromPropertyName{
	    return @{@"des": @"description"};
	}
	
像多个页面使用同一个类，页面相似，数据也相似，但字段不同的情况，我们也可以通过这种方式，免除建很多个多余的Model的烦恼。

###写在最后

最后分享一个根据字典的key快速创建模型的方法。

	- (void)createModelWithDictionary:(NSDictionary *)dict {
	    for (NSString *key in dict) {
	        printf("@property (nonatomic, copy) NSString *%s;\n", [key UTF8String]);
	    }
	}
	
然后将XCode打印的数据复制粘贴到我们的数据模型类里，偷懒也需要讲究方法不是么？



