title: "Objective-C笔记之正则表达式"

date: 2016-05-20 12:03:42

description: 

categories: iOS开发 - 基础

tags: [iOS,开发,笔记]

---

正则表达式从本质上来讲是一种描述字符串匹配模式的方式。它可以用来检查一个字符串是否包含某个子串，将检查的子串做替换，或者从某个串中取出符合某个条件的子串等。

<!--more-->

### 正则表达式介绍

正则表达式是由普通字符（例如字符 a 到 z）以及特殊字符（称为"元字符"）组成的文字模式。其中普通字符包括大小写的字母和数字，而元字符则具有特殊的含义。常用的元字符有：

元字符 				| 描述 			 
---------- 		|----------	
^     		| 匹配输入字符串的开始位置。    
$     		| 匹配输入字符串的结束位置。    
{n}     	| n是一个非负整数，匹配确定的n次。例如，“o{2}”不能匹配“Bob”中的“o”，但是能匹配“food”中的两个o。    
{n,}    	| n是一个非负整数。至少匹配n次。例如，“o{2,}”不能匹配“Bob”中的“o”，但能匹配“foooood”中的所有o。    
{n,m}     | m和n均为非负整数，其中n<=m。最少匹配n次且最多匹配m次。   
[^xyz]		| 负值字符集合。匹配未包含的任意字符。例如，“[^abc]”可以匹配“plain”中的“plin”。   
[a-z]		| 字符范围。匹配指定范围内的任意字符。例如，“[a-z]”可以匹配“a”到“z”范围内的任意小写字母字符。   
[^a-z]		| 负值字符范围。匹配任何不在指定范围内的任意字符。例如，“[^a-z]”可以匹配任何不在“a”到“z”范围内的任意字符。   

### 一些常用的正则表达式

**关于字符串匹配**

判断`首字符是否为字母`：[A-Za-z]+

匹配`首尾空白字符`的正则表达式：^\s*|\s*$

匹配由`数字、字母或下划线`组成的字符串：^\w+$　　

匹配由`字母`组成的字符串：^[A-Za-z]+$

匹配由`大写字母`组成的字符串：^[A-Z]+$

匹配由`小写字母`组成的字符串：^[a-z]+$

匹配由`数字和2字母`组成的字符串：^[A-Za-z0-9]+$

匹配`空白行`的正则表达式：\n\s*\r

匹配`中文字符`的正则表达式：[\u4e00-\u9fa5]

**关于账号合法性**

手机号：^[1][3578][0-9]{9}$

邮箱地址：[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,4} 

身份证：\d{15}|\d{18}

用户帐号(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$

### OC中正则表达式的使用

**判断手机号是否合法**

	#pragma mark -- 判断手机是否合法
	- (BOOL)validateMobile:(NSString *)mobile
	{
	   	//手机号以13，15，17，18开头，后面加9位数字
	   	NSString *phoneRegex = @"^[1][3578][0-9]{9}$";
	    NSPredicate *phoneTest = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",phoneRegex];
	    return [phoneTest evaluateWithObject:mobile];
	}

**判断邮箱是否合法**

	#pragma mark -- 判断邮箱是否合法
	- (BOOL)validateEmail:(NSString *)email
	{
	    NSString *emailRegex = @"[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{1,5}";
	    NSPredicate *emailTest = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",emailRegex];
	    return [emailTest evaluateWithObject:email];
	}

**筛选数组array中包含string的字符串**

	#pragma mark -- 筛选数组array中包含string的字符串
	- (NSArray *)filterArray:(NSArray *)array string:(NSString *)string
	{
	    NSPredicate *pred = [NSPredicate predicateWithFormat:@"SELF CONTAINS %@",string];
	    return [array filteredArrayUsingPredicate:pred];
	}



### NSPredicate对象

**相关介绍**

NSPredicate对象主要用于查询和过滤，它可以指定查询和过滤条件，然后从集合中分拣出符合条件的对象，原理有点类似于SQL中的where。当然也可以用于字符串的正则匹配。使用步骤主要有：

	// 创建NSPredicate（相当于创建一个过滤条件）  
	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"过滤条件"];
	  
	// 判断指定的对象是否满足NSPredicate创建的过滤条件  
	[predicate evaluateWithObject:person]; 
	 
	// 过滤出符合条件的对象（返回所有符合条件的对象）  
	NSArray *users = [array filteredArrayUsingPredicate:predicate]; 

**常用条件**

* 逻辑运算符号： >，<，=，>=，<=，&&，||，AND， OR
* IN
* 以xx开头：BEGINSWITH
* 以xx结尾：ENDSWITH
* 包含：CONTAIN
* 模糊查询：LIKE
* 指代对象本身：SELF
* 正则表达式：MATCHES

### NSRegularExpression对象介绍

这是苹果推出的官方正则表达式，很方便功能也很强大。[官方文档传送门](https://developer.apple.com/library/mac/documentation/Foundation/Reference/NSRegularExpression_Class/index.html#//apple_ref/occ/instm/NSRegularExpression/enumerateMatchesInString:options:range:usingBlock:)

**常用方法**

* 返回所有匹配结果的集合

	`- (NSArray *)matchesInString:(NSString *)string options:(NSMatchingOptions)options range:(NSRange)range;`
 
* 返回正确匹配的个数

	`- (NSUInteger)numberOfMatchesInString:(NSString *)string options:(NSMatchingOptions)options range:(NSRange)range;`
	
* 返回第一个匹配的结果，匹配的结果保存在NSTextCheckingResult类型中

	`- (NSTextCheckingResult *)firstMatchInString:(NSString *)string options:(NSMatchingOptions)options range:(NSRange)range;`
	
* 返回第一个正确匹配结果字符串的NSRange

	`- (NSRange)rangeOfFirstMatchInString:(NSString *)string options:(NSMatchingOptions)options range:(NSRange)range;`
 
**使用步骤**

以下皆为伪代码，只为做说明。

	// 根据匹配条件,创建了一个正则表达式  
	NSRegularExpression *regex = [NSRegularExpression regularExpressionWithPattern:@"^[1][3578][0-9]{9}$" options:NSRegularExpressionCaseInsensitive error:&error];
	  
	// 根据需要调用不同的方法 
	NSUInteger number = [regex numberOfMatchesInString:urlString
	                                           options:0
	                                             range:NSMakeRange(0, [urlString length])]; 

### 参考

[正则表达式 - 语法](http://www.runoob.com/regexp/regexp-syntax.html)

[OC中正则表达式的使用](http://blog.csdn.net/lianbaixue/article/details/10579117)

[NSPredicate对象基本用法](http://blog.csdn.net/daiyelang/article/details/18546405)

[NSRegularExpression对象使用](http://www.tuicool.com/articles/RBnqqeQ)
