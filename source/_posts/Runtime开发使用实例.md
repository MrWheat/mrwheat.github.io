title: "Runtime开发使用实例"

date: 2016-08-12 10:45:23

description:

categories: iOS笔记

tags: [iOS,runtime]

---

当我们学习和使用Objective-C时，Objective-C Runtime往往被我们自动忽略了。因为就算不知道它的存在，似乎对我们平时写代码并没有多大的影响。既然没影响，那我们还学它干嘛，就像不会造车但我会开车不就可以了？

但是当你在这行逐步深入进去，你至少应该知道一些Runtime的工作细节，知道Objective-C正在做的事情，能够帮助你更深层次的理解Objective-C和你的App，不管你有没有好奇心，对你个人来说也是一种收获和提升。了解车的性能和构造又何尝不是为了能更好的驾驭这辆车，成为一名资深的赛车手。

<!--more-->

### 什么是runtime

概念这东西，都是老生常谈，引用网上的话：Objective-C的Runtime是一个运行时库（Runtime Library），主要使用 C 和汇编编写，为C添加了面相对象的能力并创造了 Objective-C。

### runtime的作用

通过runtime，你可以动态的产生、修改和删除一个类，或者一个方法和成员变量。

### runtime的使用

使用之前，首先包含头文件。

	 #import <objc/runtime.h>	// 包含对类、成员变量、属性、方法的操作
	 #import <objc/message.h>	// 包含消息机制
	 
以下是runtime经常用到的方法。

	class_copyIvarList()	// 返回一个指向类的成员变量数组的指针
	class_copyPropertyList()	//返回一个指向类的属性数组的指针
	ivar_getName()	// 获取成员变量名（C类型的字符串）
	property_getName()	// 获取属性名（C类型的字符串）
	---------------------------------------------------
	typedef struct objc_method *Method;
	class_getInstanceMethod()
	class_getClassMethod()	// 以上两个函数传入返回Method类型
	---------------------------------------------------
	method_exchangeImplementations(Method m1, Method m2)	// 交换两个方法的实现
	
#### 获取类成员变量

通过runtime可以获取某个类的所有成员变量，代码如下，使用时需要添加头文件`#import <objc/runtime.h>`

	/**
	 *  获取某个类的所有成员变量
	 *
	 *  @param object 某个类的实例对象
	 *
	 *  @return NSArray（成员变量名和值）
	 */
	- (NSArray *)getAllIvar:(id)object
	{
	    NSMutableArray *array = [NSMutableArray array];
	    unsigned int count;
	    // 获得一个指向该类成员变量的指针
	    Ivar *ivars = class_copyIvarList([object class], &count);
	    for (int i = 0; i < count; i++) {
	        Ivar ivar = ivars[i];
	        // 根据ivar获得其成员变量的名称(C语言字符串)
	        const char *keyChar = ivar_getName(ivar);
	        NSString *keyStr = [NSString stringWithCString:keyChar encoding:NSUTF8StringEncoding];
	        @try {
	            id valueStr = [object valueForKey:keyStr];
	            NSDictionary *dic = nil;
	            if (valueStr) {
	                dic = @{keyStr : valueStr};
	            } else {
	                dic = @{keyStr : @"值为nil"};
	            }
	            [array addObject:dic];
	        }
	        @catch (NSException *exception) {}
	    }
	    return [array copy];
	}
	
我们以UILabel为例：
	
![](/img/Runtime开发使用实例01.png)

#### 获取类属性

获取类属性的方式跟获取类成员变量的方式类似，代码如下。

	/**
	 *  获取某个类的所有属性
	 *
	 *  @param object 某个类的实例对象
	 *
	 *  @return NSArray（所有属性名）
	 */
	- (NSArray *)getAllProperty:(id)object
	{
	    NSMutableArray *array = [NSMutableArray array];
	    unsigned int count;
	    // 获得指向该类所有属性的指针
	    objc_property_t *propertys = class_copyPropertyList([object class], &count);
	    for (int i = 0; i < count; i++) {
	        objc_property_t property = propertys[i];
	        // 通过objc_property_t获得属性名称(C语言字符串)
	        const char *nameChar = property_getName(property);
	        NSString *nameStr = [NSString stringWithCString:nameChar encoding:NSUTF8StringEncoding];
	        [array addObject:nameStr];
	    }
	    return [array copy];
	}
	
同样以UILabel为例：
	
![](/img/Runtime开发使用实例02.png)
	
### 应用实例

我们可以通过runtime来获得某个类的所有属性和所有成员变量，便可以通过这种方式实现对任意类的快速归档和解档，同样的，还能实现字典转模型的操作。

#### 归档解档

关于自定义类的归档需要遵守<NSCoding>协议，使用runtime同时也别忘记包含头文件。

	#import "MZTestModel.h"
	#import <objc/runtime.h>
	@interface MZTestModel () <NSCoding>
	@end
	
然后实现NSCoding的协议方法，这里我们通过runtime动态获取该类的属性，通过这种方式，我们不用关心类里到底有多少属性，属性名称是什么，一套代码就能搞定自定义类的归档和解档。

	- (void)encodeWithCoder:(NSCoder *)encoder{
	    //归档存储自定义对象
	    unsigned int count = 0;
	    //获得指向该类所有属性的指针
	    objc_property_t *properties =     class_copyPropertyList([self class], &count);
	    for (int i =0; i < count; i ++) {
	        objc_property_t property = properties[i];
	        // 根据objc_property_t获得其属性的名称(C语言的字符串)
	        const char *name = property_getName(property);
	        NSString *key = [NSString   stringWithUTF8String:name];
	        // 编码每个属性,利用kVC取出每个属性对应的数值
	        [encoder encodeObject:[self valueForKeyPath:key] forKey:key];
	    }}
	
	- (instancetype)initWithCoder:(NSCoder *)decoder{
	    // 归档存储自定义对象
	    unsigned int count = 0;
	    // 获得指向该类所有属性的指针
	    objc_property_t *properties = class_copyPropertyList([self class], &count);
	    for (int i =0; i < count; i ++) {
	        objc_property_t property = properties[i];
	        // 根据objc_property_t获得其属性的名称(C语言的字符串)
	        const char *name = property_getName(property);
	        NSString *key = [NSString stringWithUTF8String:name];
	        // 解码每个属性,利用kVC取出每个属性对应的数值
	        [self setValue:[decoder decodeObjectForKey:key] forKeyPath:key];
	    }
	    return self;
	}

#### 交换方法

交换方法是通过`method_exchangeImplementations(Method m1, Method m2)`实现的。对于苹果系统提供的方法，我们看不到其内部实现，当然也无法去修改里面的代码。但是却可以使用runtime去替换它，这样就能处理某些使用系统方法而导致程序崩溃的问题(当然使用继承也是可以解决这个问题的)。

举个例子，我们在使用字典和数组的时候，是不能向里面写入nil的。这样会导致程序崩溃。

在进行后台接口调试的时候，经常会遇到这个问题。比如登陆注册，我们需要将用户的账号和密码以字典的形式传给后台进行比对，如果账号密码为空，前端不进行判断，直接将该值写入字典，那么程序就直接崩溃了。

有些接口的参数比较多，同时部分字段又不是必传字段。我们需要每次对传入的数据进行判断是否为空，然后才写入字典，很麻烦，因为你不判断程序就直接崩溃了，而一堆if语句看起来又是那么的恶心。。。。

那么如何解决？我们以NSMutableArray为例。

我在数组里加了一个nil，代码直接崩了。

![](/img/Runtime开发使用实例03.png)

然后我们给NSMutableArray新建一个类别Extension，然后在类别里添加以下方法。

	// 该方法在类或者分类第一次加载内存时会自动调用
	+ (void)load {
	    // __NSArrayM为NSMutableArray对象的类型，通过以下方式获取。
	    // NSMutableArray *testArray = [[NSMutableArray alloc] init];
	    // NSLog(@"%@", NSStringFromClass([testArray class]));
	    Method oldMethod = class_getInstanceMethod(NSClassFromString(@"__NSArrayM"), @selector(addObject:));
	    Method newMethod = class_getInstanceMethod(NSClassFromString(@"__NSArrayM"), @selector(mz_addObject:));
	    method_exchangeImplementations(oldMethod, newMethod);
	}
	
	// 自定义方法
	- (void)mz_addObject:(id)object {
	    if (object != nil) {
	        [self mz_addObject:object];
	    }
	}

![](/img/Runtime开发使用实例04.png)

再一次运行程序，一切正常，没有崩溃。

![](/img/Runtime开发使用实例05.png)

### 参考

[理解 Objective-C Runtime](http://www.cocoachina.com/ios/20141008/9844.html)

