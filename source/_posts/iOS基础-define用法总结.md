title: "#define用法总结"

date: 2015-05-21 14:00:27

description: 

categories: iOS开发 - 基础

tags: [笔记,语法,基础]

---

学好C和OC，漂亮的宏定义很重要，使用好宏定义，不仅能减少代码出错机率，提高可读性，同时方便移植，代码看起来也简洁美观，何乐而不为呢。

<!--more-->

我们经常在程序中反复使用的地方使用宏定义。

当然我们也可以重新定义一些类型，防止由于各种平台和编译器的不同，而产生的类型字节数差异，方便移植，当然定义类型使用最多的还是typedef。

`#define uint16 unsigned int`

`typedef	unsigned long int  uint32;`

常用的宏定义有两种：`带参数的宏定义`和`不带参数的宏定义`。

### 不带参数的宏定义

* 使用格式：#define	宏名	字符串
* 作用：将程序中所有出现宏名的地方都用字符串进行替换
* 例子：#define	SIZE	100

### 带参数的宏定义

* 使用格式：#define	宏名(行参列表)	字符串
* 作用：用实参替换行参，其他字符串原样保留
* 例子：#define S(a, b)	((a)*(b))
* 注意：字符串中参数最好加上括号，防止出现由优先级导致的bug。
* 例子：S(3+2, 5+7);	—>	3+2*5+7	—>	20

### 宏定义#和##用法

* "#"是"字符串化"的意思。出现在宏定义中的"#"是把跟在后面的参数转换成一个字符串。

	```objectic
	#define STRCPY(dst, src)   strcpy(dst, #src)
	STRCPY(buff, abc) 相当于 strcpy(buff, "abc")
	```

* "##"是一种分隔连接方式，它的作用是先分隔，然后进行强制连接。

	```objectic
	#define CONNECT(x) T_##x
	CONNECT(abc) 相当于 T_abc
	```

	值得注意的是如果##后的参数本身也是一个宏的话，##会阻止这个宏的展开，也就是只替换一次。
	
	```objectivec
	#define STRCPY(a, b)	strcpy(a##_p, #b)
	int main()
	{
		STRCPY(STRCPY(var1,var2),var2);
		// 这里是否会展开为： strcpy(strcpy(var1_p,"var2")_p,"var2“）？
		// 答案是否定的：
		// 展开结果将是：  strcpy(STRCPY(var1,var2)_p,"var2")
		// ## 阻止了参数的宏展开!
		// 如果宏定义里没有用到 # 和 ##, 宏将会完全展开
	} 
	```

### 多行宏定义

在需要换行的地方使用"/"

```objectivec
#define MACRO(arg1, arg2) do { /
	stmt1; /
	stmt2; /
} while(0)
```

### 常用预处理命令

* 防止一个头文件重复包含

	```objectivec
	#ifndef COMDEF_H
	#define COMDEF_H
	  //头文件内容
	#endif
	```

* 条件编译（版本测试）

	```objectivec
	#define DEBUG
	//...
	#ifdef BEBUG
	//debugging code here
	#else
	//...
	#endif
	```


