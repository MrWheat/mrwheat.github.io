title: "iOS教程之Block的使用"
date: 2015-07-19 14:10:18
description:
categories: iOS开发 - 基础
tags: [笔记,用法,iOS]

---

代码块本质上是和其他变量类似。不同的是，代码块存储的数据是一个函数体。

Block代码块和C语言中的函数指针类似，Objective-C中的SEL也与函数指针类似。函数指针用来保存一个C函数的地址，SEL用来保存一个成员函数的地址，而Block则用来保存一个代码块的地址。使用代码块时，你可以像调用其他标准函数一样，传入参数，并得到返回值。脱字符（^）是块的语法标记。

<!--more-->

### 创建一个简单的block

代码：

```objectivec
// 定义了一个无参数，返回类型为void的printBlock的变量。
// 这个变量，就可以保存一个代码块的首地址。
void(^printBlock)(void);
// printBlock是一个变量，我们可以给这个变量赋值。
printBlock = ^(void) {
	NSLog(@"hello world!");
};
// Block代码块调用方式和方法的调用方式相同
printBlock();
// printBlock算是一个变量，我们可以更改这个变量的值
printBlock = ^ {
	NSLog(@"hello block!");
};
printBlock();

```

运行结果：

```objectivec
2016-05-19 15:59:59.157 blogTestCode[15443:285677] hello world!
2016-05-19 15:59:59.157 blogTestCode[15443:285677] hello block!
```

### 创建多参数的block

代码：

```objectivec
int (^calculateNum)(int x, int y);
// 代码块的返回值类型通过return体现出来
// 这里只是赋值不是调用
calculateNum  = ^(int x, int y) {
	int num = x + y;
	return num;
};
NSLog(@"%d", calculateNum(1, 2));

```

运行结果：

```objectivec
2016-05-19 15:48:30.843 blogTestCode[15415:275933] 3
```

### 创建参数为OC对象的block

代码：

```objectivec
// 定义变量的时候直接初始化
NSString *(^splitString)(NSString *, NSString *) = ^(NSString *string1, NSString *string2) {
	NSString *splitString = [string1 stringByAppendingString:string2];
	return splitString;
};
NSString *string = splitString(@"hello", @" block");
NSLog(@"%@", string);

```

运行结果：

```objectivec
2016-05-19 16:06:02.437 blogTestCode[15478:290573] hello block
```

### 调用系统的代码块进行数组排序

代码：

```objectivec
NSArray *stringArray = [NSArray arrayWithObjects:@"abc 2", @"abc 5", @"abc 1",@"abc 4",@"abc 3",nil];
// NSComparator为系统自定义的Block类型
NSComparator sortBlock = ^(id string1, id string2) {
	return [string1 compare:string2];
};
NSArray *sortArray = [stringArray sortedArrayUsingComparator:sortBlock];
NSLog(@"%@", sortArray);

```

运行结果：

```objectivec
2016-05-19 16:16:12.683 blogTestCode[15499:296615] (
    "abc 1",
    "abc 2",
    "abc 3",
    "abc 4",
    "abc 5"
)
```

### 自定义Block类型

代码：

```objectivec
// 首先在文件最上方定义Block类型
typedef NSComparisonResult (^SortDataArray)(NSString *, NSString *);

// 然后在方法中通过类型定义变量
NSArray *stringArray = [NSArray arrayWithObjects:@"abc 2", @"abc 5", @"abc 1",@"abc 4",@"abc 3",nil];
SortDataArray sortBlock = ^(NSString *string1, NSString *string2) {
	return [string1 compare:string2];
};
// 如果参数是方法则使用sortUsingSelector方法进行排序
NSArray *sortArray = [stringArray sortedArrayUsingComparator:sortBlock];
NSLog(@"%@", sortArray);

```

运行结果：

```objectivec
2016-05-19 16:34:51.499 blogTestCode[15617:310741] (
    "abc 1",
    "abc 2",
    "abc 3",
    "abc 4",
    "abc 5"
)
```


### 常见错误

##### 在代码块中使用局部变量

代码：

```objectivec
// 局部变量
int num = 5;
// 在对block进行赋值的时候，block里的num，已经被进行了一个值拷贝。
void (^addNum)() = ^ {
	NSLog(@"%d", num);
};
// 改变num的值
num = num + 5;
// 调用Block打印num，打印的结果不是10，而是5！
addNum();

```

运行结果：

```objectivec
2016-05-19 16:43:34.237 blogTestCode[15694:318406] 5
```

##### 在代码块中使用全局变量

代码：

```objectivec
// 全局变量
int num = 5;
- (void)testMethod {
	// 全局变量不进行值拷贝
    void (^addNum)() = ^ {
        NSLog(@"%d", num);
    };
    // 改变num的值
    num = num + 5;
    // 打印num，结果是10！
    addNum();
}

```

运行结果：

```objectivec
2016-05-19 16:43:34.237 blogTestCode[15694:318406] 10
```

##### 在代码块中改变局部变量

代码：

```objectivec
// 局部变量需要加__block前缀，否则会报错。而且block中的num是自己定义的num，可以在代码块中改变num的值。
__block int num = 5;
void (^addNum)() = ^ {
	// 改变num的值
	num ++;
	NSLog(@"A:%d", num);
};
NSLog(@"B:%d", num);
// 调用block
addNum();
NSLog(@"C:%d", num);

```

运行结果：

```objectivec
2016-05-19 16:59:29.609 blogTestCode[15748:330854] B:5
2016-05-19 16:59:29.610 blogTestCode[15748:330854] A:6
2016-05-19 16:59:29.610 blogTestCode[15748:330854] C:6
```

##### 在代码块中改变全局变量

代码：

```objectivec
// 全局变量不需要加block修饰，而且block中的num是自己定义的num，可以在代码块中改变num的值。
int num = 5;
- (void)testMethod {
	void (^addNum)() = ^ {
    	// 改变num的值
		num ++;
		NSLog(@"A:%d", num);
	};
	NSLog(@"B:%d", num);
	addNum();
	NSLog(@"C:%d", num);
}

```

运行结果：

```objectivec
2016-05-19 17:07:55.873 blogTestCode[15763:336709] B:5
2016-05-19 17:07:55.873 blogTestCode[15763:336709] A:6
2016-05-19 17:07:55.873 blogTestCode[15763:336709] C:6
```

##### 代码块的递归调用

代码块想要递归调用，代码块变量必须是全局变量或者是静态变量，这样在程序启动的时候代码块变量就初始化了，从而才能进行递归调用。





