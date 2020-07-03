title: "iOS教程之AutoLayout/SizeClass屏幕适配"

date: 2015-1-06 20:27:12

description:

categories: iOS开发 - 基础

tags: [笔记,iOS]

---

随着iOS设备的成员不断增多，屏幕分辨率也在不断的变多，在开发过程中如果使用固定尺寸为每种屏幕开发一套固定的代码，会大大增加工作量，这时候需要使用到屏幕适配。屏幕适配是在同一代码下通过调节UI，使之使用适应于各种不同分辨率的设备。而AutoLayout是一种基于约束的，描述性的布局系统（官方定义:AutoLayout Is a Constraint-Based, Descriptive Layout System）。它是让UI适应控件自适应设备尺寸变化的一项关键技术。

<!--more-->

### 目录

1. AutoLayout基本使用
2. Massory第三方库
3. SizeClass使用

### Autolayout基本使用

* 控件靠上对齐

	设置方式:
	
	![](/img/iOS基础-AutoLayout自动布局/top.png)
	
	效果图:
	
	iphone4/4S适应---iphone5/5S适应---iphone6/6S适应:
	
	![](/img/iOS基础-AutoLayout自动布局/top-i4.png) ![](/img/iOS基础-AutoLayout自动布局/top-i5.png) ![](/img/iOS基础-AutoLayout自动布局/top-i6.png)

* 控件靠下对齐

	设置方式:
	
	![](/img/iOS基础-AutoLayout自动布局/bottom.png)
	
	效果图:
	
	iphone4/4S适应---iphone5/5S适应---iphone6/6S适应:
	
	![](/img/iOS基础-AutoLayout自动布局/bottom-i4.png) ![](/img/iOS基础-AutoLayout自动布局/bottom-i5.png) ![](/img/iOS基础-AutoLayout自动布局/bottom-i6.png)

* 控件靠左对齐

	设置方式:
	
	![](/img/iOS基础-AutoLayout自动布局/left.png)
	
	效果图:
	
	iphone4/4S适应---iphone5/5S适应---iphone6/6S适应:
	
	![](/img/iOS基础-AutoLayout自动布局/left-i4.png) ![](/img/iOS基础-AutoLayout自动布局/left-i5.png) ![](/img/iOS基础-AutoLayout自动布局/left-i6.png)

* 控件靠右对齐

	设置方式:
	
	![](/img/iOS基础-AutoLayout自动布局/right.png)
	
	效果图:
	
	iphone4/4S适应---iphone5/5S适应---iphone6/6S适应:
	
	![](/img/iOS基础-AutoLayout自动布局/right-i4.png) ![](/img/iOS基础-AutoLayout自动布局/right-i5.png) ![](/img/iOS基础-AutoLayout自动布局/right-i6.png)

* 控件居中对齐

	设置方式:
	
	![](/img/iOS基础-AutoLayout自动布局/center.png)
	
	效果图:
	
	iphone4/4S适应---iphone5/5S适应---iphone6/6S适应:
	
	![](/img/iOS基础-AutoLayout自动布局/center-i4.png) ![](/img/iOS基础-AutoLayout自动布局/center-i5.png) ![](/img/iOS基础-AutoLayout自动布局/center-i6.png)

* 控件适应容器大小

	设置方式:
	
	![](/img/iOS基础-AutoLayout自动布局/auto.png)
	
	效果图:
	
	iphone4/4S适应---iphone5/5S适应---iphone6/6S适应:
	
	![](/img/iOS基础-AutoLayout自动布局/auto-i4.png) ![](/img/iOS基础-AutoLayout自动布局/auto-i5.png) ![](/img/iOS基础-AutoLayout自动布局/auto-i6.png)

* 控件之间约束

	![](/img/iOS基础-AutoLayout自动布局/竖屏.png) ![](/img/iOS基础-AutoLayout自动布局/横屏.png)

### Massory第三方库

#### 基本使用

Massory第三方库的基本属性
	
	//左侧
	@property (nonatomic, strong, readonly) MASConstraint *left;
	//上侧
	@property (nonatomic, strong, readonly) MASConstraint *top;
	//右侧
	@property (nonatomic, strong, readonly) MASConstraint *right;
	//下侧
	@property (nonatomic, strong, readonly) MASConstraint *bottom;
	//首部
	@property (nonatomic, strong, readonly) MASConstraint *leading;
	//尾部
	@property (nonatomic, strong, readonly) MASConstraint *trailing;
	//宽
	@property (nonatomic, strong, readonly) MASConstraint *width;
	//高
	@property (nonatomic, strong, readonly) MASConstraint *height;
	//横向中点
	@property (nonatomic, strong, readonly) MASConstraint *centerX;
	//纵向中点
	@property (nonatomic, strong, readonly) MASConstraint *centerY;
	//文本基线
	@property (nonatomic, strong, readonly) MASConstraint *baseline;
	
其中leading与left trailing与right 在正常情况下是等价的 但是当一些布局是从右至左时(比如阿拉伯文?没有类似的经验) 则会对调 换句话说就是基本可以不理不用 用left和right就好了
	
在ios8发布后 又新增了一堆奇奇怪怪的属性(有兴趣的朋友可以去瞅瞅) Masonry暂时还不支持(不过你要支持ios6,ios7 就没必要去管那么多了)
	
在讲实例之前 先介绍一个MACRO
	
	#define WS(weakSelf)  __weak __typeof(&*self)weakSelf = self;
	
快速的定义一个weakSelf 当然是用于block里面啦 下面进入正题(为了方便 我们测试的superView都是一个size为(300,300)的UIView)
	
下面 通过一些简单的实例来简单介绍如何轻松愉快的使用Masonry:

1. [基础] 居中显示一个view

		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    // Do any additional setup after loading the view.
			
		    WS(ws);
			
		    UIView *sv = [UIView new];
		    [sv showPlaceHolder];
		    sv.backgroundColor = [UIColor blackColor];
		    [self.view addSubview:sv];
		    [sv mas_makeConstraints:^(MASConstraintMaker *make) {
		        make.center.equalTo(ws.view);
		        make.size.mas_equalTo(CGSizeMake(300, 300));
		    }];
		}
		
	代码效果
		
	![](/img/iOS基础-AutoLayout自动布局/300*300.png)
		
	使用我之间写的MMPlaceHolder 可以看到superview已经按照我们预期居中并且设置成了适当的大小
		
	那么先看看这几行代码
		
		//从此以后基本可以抛弃CGRectMake了
		UIView *sv = [UIView new];
		//在做autoLayout之前 一定要先将view添加到superview上 否则会报错
		[self.view addSubview:sv];
		//mas_makeConstraints就是Masonry的autolayout添加函数 将所需的约束添加到block中行了
		[sv mas_makeConstraints:^(MASConstraintMaker *make) {
		    //将sv居中(很容易理解吧?)
		    make.center.equalTo(ws.view);
			
		    //将size设置成(300,300)
		    make.size.mas_equalTo(CGSizeMake(300, 300));
		}];
		
	这里有两个问题要分解一下
		
	首先在Masonry中能够添加autolayout约束有三个函数
		
		- (NSArray *)mas_makeConstraints:(void(^)(MASConstraintMaker *make))block;
		- (NSArray *)mas_updateConstraints:(void(^)(MASConstraintMaker *make))block;
		- (NSArray *)mas_remakeConstraints:(void(^)(MASConstraintMaker *make))block;
		/*
		mas_makeConstraints 只负责新增约束 Autolayout不能同时存在两条针对于同一对象的约束 否则会报错 
		mas_updateConstraints 针对上面的情况 会更新在block中出现的约束 不会导致出现两个相同约束的情况
		mas_remakeConstraints 则会清除之前的所有约束 仅保留最新的约束
		三种函数善加利用 就可以应对各种情况了
		*/
		
	其次 equalTo 和 mas_equalTo的区别在哪里呢? 其实 mas_equalTo是一个MACRO
		
		#define mas_equalTo(...)                 equalTo(MASBoxValue((__VA_ARGS__)))
		#define mas_greaterThanOrEqualTo(...)    greaterThanOrEqualTo(MASBoxValue((__VA_ARGS__)))
		#define mas_lessThanOrEqualTo(...)       lessThanOrEqualTo(MASBoxValue((__VA_ARGS__)))
		#define mas_offset(...)                  valueOffset(MASBoxValue((__VA_ARGS__)))
		
	可以看到 mas_equalTo只是对其参数进行了一个BOX操作(装箱) MASBoxValue的定义具体可以看看源代码 太长就不贴出来了
		
	所支持的类型 除了NSNumber支持的那些数值类型之外 就只支持CGPoint CGSize UIEdgeInsets
		
	介绍完这几个问题 我们就继续往下了 PS:刚才定义的sv会成为我们接下来所有sample的superView

2. [初级] 让一个view略小于其superView(边距为10)

		UIView *sv1 = [UIView new];
		[sv1 showPlaceHolder];
		sv1.backgroundColor = [UIColor redColor];
		[sv addSubview:sv1];
		[sv1 mas_makeConstraints:^(MASConstraintMaker *make) {
		make.edges.equalTo(sv).with.insets(UIEdgeInsetsMake(10, 10, 10, 10));
		
		/* 等价于
		make.top.equalTo(sv).with.offset(10);
		make.left.equalTo(sv).with.offset(10);
		make.bottom.equalTo(sv).with.offset(-10);
		make.right.equalTo(sv).with.offset(-10);
		*/
		
		/* 也等价于
		make.top.left.bottom.and.right.equalTo(sv).with.insets(UIEdgeInsetsMake(10, 10, 10, 10));
		*/
		}];
	
	代码效果
	
	![](/img/iOS基础-AutoLayout自动布局/280*280.png)
	
	可以看到 edges 其实就是top,left,bottom,right的一个简化 分开写也可以 一句话更省事
	
	那么为什么bottom和right里的offset是负数呢? 因为这里计算的是绝对的数值 计算的bottom需要小鱼sv的底部高度 所以要-10 同理用于right
	
	这里有意思的地方是and和with 其实这两个函数什么事情都没做
	
		- (MASConstraint *)with {
		    return self;
		}
		- (MASConstraint *)and {
		    return self;
		}
	
	但是用在这种链式语法中 就非常的巧妙和易懂 不得不佩服作者的心思(虽然我现在基本都会省略)

3. [初级] 让两个高度为150的view垂直居中且等宽且等间隔排列 间隔为10(自动计算其宽度)
	
		int padding1 = 10;
		[sv2 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.centerY.mas_equalTo(sv.mas_centerY);
		    make.left.equalTo(sv.mas_left).with.offset(padding1);
		    make.right.equalTo(sv3.mas_left).with.offset(-padding1);
		    make.height.mas_equalTo(@150);
		    make.width.equalTo(sv3);
		}];
		[sv3 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.centerY.mas_equalTo(sv.mas_centerY);
		    make.left.equalTo(sv2.mas_right).with.offset(padding1);
		    make.right.equalTo(sv.mas_right).with.offset(-padding1);
		    make.height.mas_equalTo(@150);
		    make.width.equalTo(sv2);
		}];
	
	代码效果
	
	![](/img/iOS基础-AutoLayout自动布局/135*150.png)
	
	这里我们在两个子view之间互相设置的约束 可以看到他们的宽度在约束下自动的被计算出来了。

4. [中级] 在UIScrollView顺序排列一些view并自动计算contentSize
	
		UIScrollView *scrollView = [UIScrollView new];
		scrollView.backgroundColor = [UIColor whiteColor];
		[sv addSubview:scrollView];
		[scrollView mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.edges.equalTo(sv).with.insets(UIEdgeInsetsMake(5,5,5,5));
		}];
		UIView *container = [UIView new];
		[scrollView addSubview:container];
		[container mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.edges.equalTo(scrollView);
		    make.width.equalTo(scrollView);
		}];
		int count = 10;
		UIView *lastView = nil;
		for ( int i = 1 ; i <= count ; ++i )
		{
		    UIView *subv = [UIView new];
		    [container addSubview:subv];
		    subv.backgroundColor = [UIColor colorWithHue:( arc4random() % 256 / 256.0 )
		    saturation:( arc4random() % 128 / 256.0 ) + 0.5
		    brightness:( arc4random() % 128 / 256.0 ) + 0.5
		    alpha:1];
		
		    [subv mas_makeConstraints:^(MASConstraintMaker *make) {
		        make.left.and.right.equalTo(container);
		        make.height.mas_equalTo(@(20*i));
		
		        if (lastView) {
		            make.top.mas_equalTo(lastView.mas_bottom);
		        } else {
		            make.top.mas_equalTo(container.mas_top);
		        }
		    }];
		    lastView = subv;
		}
		[container mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.bottom.equalTo(lastView.mas_bottom);
		}];
	
	代码效果
	
	头部效果---尾部效果
	
	![](/img/iOS基础-AutoLayout自动布局/head.png) ![](/img/iOS基础-AutoLayout自动布局/tail.png)
	
	从scrollView的scrollIndicator可以看出 scrollView的内部已如我们所想排列好了
	
	这里的关键就在于container这个view起到了一个中间层的作用 能够自动的计算uiscrollView的contentSize。

5. [高级] 横向或者纵向等间隙的排列一组view

	很遗憾 autoLayout并没有直接提供等间隙排列的方法(Masonry的官方demo中也没有对应的案例) 但是参考案例3 我们可以通过一个小技巧来实现这个目的 为此我写了一个Category：
		
		@implementation UIView(Masonry_LJC)
		- (void) distributeSpacingHorizontallyWith:(NSArray*)views
		{
		    NSMutableArray *spaces = [NSMutableArray arrayWithCapacity:views.count+1];
		
		    for ( int i = 0 ; i < views.count+1 ; ++i )
		    {
		        UIView *v = [UIView new];
		        [spaces addObject:v];
		        [self addSubview:v];
		
		        [v mas_makeConstraints:^(MASConstraintMaker *make) {
		            make.width.equalTo(v.mas_height);
		        }];
		    }    
		
		    UIView *v0 = spaces[0];
		
		    __weak __typeof(&*self)ws = self;
		
		    [v0 mas_makeConstraints:^(MASConstraintMaker *make) {
		        make.left.equalTo(ws.mas_left);
		        make.centerY.equalTo(((UIView*)views[0]).mas_centerY);
		    }];
		
		    UIView *lastSpace = v0;
		    for ( int i = 0 ; i < views.count; ++i )
		    {
		        UIView *obj = views[i];
		        UIView *space = spaces[i+1];
		
		        [obj mas_makeConstraints:^(MASConstraintMaker *make) {
		            make.left.equalTo(lastSpace.mas_right);
		        }];
		    
		        [space mas_makeConstraints:^(MASConstraintMaker *make) {
		            make.left.equalTo(obj.mas_right);
		            make.centerY.equalTo(obj.mas_centerY);
		            make.width.equalTo(v0);
		        }];
		        lastSpace = space;
		    }
		
		    [lastSpace mas_makeConstraints:^(MASConstraintMaker *make) {
		        make.right.equalTo(ws.mas_right);
		    }];
		
		}
		
		- (void) distributeSpacingVerticallyWith:(NSArray*)views {
		    NSMutableArray *spaces = [NSMutableArray arrayWithCapacity:views.count+1];
		
		    for ( int i = 0 ; i < views.count+1 ; ++i ) {
		        UIView *v = [UIView new];
		        [spaces addObject:v];
		        [self addSubview:v];
		        [v mas_makeConstraints:^(MASConstraintMaker *make) {
		            make.width.equalTo(v.mas_height);
		        }];
		    }
		    
		    UIView *v0 = spaces[0];
		    __weak __typeof(&*self)ws = self;
		
		    [v0 mas_makeConstraints:^(MASConstraintMaker *make) {
		        make.top.equalTo(ws.mas_top);
		        make.centerX.equalTo(((UIView*)views[0]).mas_centerX);
		    }];
		
		    UIView *lastSpace = v0;
		    for ( int i = 0 ; i < views.count; ++i ) {
		        UIView *obj = views[i];
		        UIView *space = spaces[i+1];
		
		        [obj mas_makeConstraints:^(MASConstraintMaker *make) {
		            make.top.equalTo(lastSpace.mas_bottom);
		        }];
		
		        [space mas_makeConstraints:^(MASConstraintMaker *make) {
		            make.top.equalTo(obj.mas_bottom);
		            make.centerX.equalTo(obj.mas_centerX);
		            make.height.equalTo(v0);
		        }];
		
		        lastSpace = space;
		    }
		
		    [lastSpace mas_makeConstraints:^(MASConstraintMaker *make) {
		        make.bottom.equalTo(ws.mas_bottom);
		    }];
		}
		@end
	
	简单的来测试一下
		
		UIView *sv11 = [UIView new];
		UIView *sv12 = [UIView new];
		UIView *sv13 = [UIView new];
		UIView *sv21 = [UIView new];
		UIView *sv31 = [UIView new];
		sv11.backgroundColor = [UIColor redColor];
		sv12.backgroundColor = [UIColor redColor];
		sv13.backgroundColor = [UIColor redColor];
		sv21.backgroundColor = [UIColor redColor];
		sv31.backgroundColor = [UIColor redColor];
		[sv addSubview:sv11];
		[sv addSubview:sv12];
		[sv addSubview:sv13];
		[sv addSubview:sv21];
		[sv addSubview:sv31];
		//给予不同的大小 测试效果
		[sv11 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.centerY.equalTo(@[sv12,sv13]);
		    make.centerX.equalTo(@[sv21,sv31]);
		    make.size.mas_equalTo(CGSizeMake(40, 40));
		}];
		[sv12 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.size.mas_equalTo(CGSizeMake(70, 20));
		}];
		[sv13 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.size.mas_equalTo(CGSizeMake(50, 50));
		}];
		[sv21 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.size.mas_equalTo(CGSizeMake(50, 20));
		}];
		[sv31 mas_makeConstraints:^(MASConstraintMaker *make) {
		    make.size.mas_equalTo(CGSizeMake(40, 60));
		}];
		[sv distributeSpacingHorizontallyWith:@[sv11,sv12,sv13]];
		[sv distributeSpacingVerticallyWith:@[sv11,sv21,sv31]];
		[sv showPlaceHolderWithAllSubviews];
		[sv hidePlaceHolder];
	
	代码效果
	
	![](/img/iOS基础-AutoLayout自动布局/final.png)
	
	简洁明了的达到了我们所要的效果
	
	这里所用的技巧就是 使用空白的占位view来填充我们目标view的旁边 这点通过图上的空白标注可以看出来

### SizeClass使用

* 屏幕适配的发展历程 
	
	代码计算frame -> autoreszing(父控件和子控件的关系ios6) -> autolayout(任何控件都可以产生关系ios7) -> sizeclass（ios8）

* Sizeclass的作用

	iOS8中为了解决屏幕适配，引入了一个全新的概念，Sizeclass 而这个使用的时候不需要判断屏幕的尺寸，也没有横屏和竖屏的概念，只有（Compact、Any，Regular）的概念！在Xcode新建的项目中，看到的storyboard界面下面栏目中，有一个（wAny,hAny）点击之后会出现一个可选择的类似九宫格的表格，这个就是一个屏幕选择器，可以对不同屏幕进行选择！

	![](/img/iOS基础-AutoLayout自动布局/1.png)
	
	仅仅是对屏幕进行了分类, 真正排布UI元素还得使用autolayout
	
	不再有横竖屏的概念, 只有屏幕尺寸的概念
	
	不再有具体尺寸的概念, 只有抽象尺寸的概念
	
	把宽度和高度各分为3种情况 而3*3的组合就是9中情况 
	
	①Compact : 紧凑(小) 
	
	②Any : 任意 
	
	③Regular : 宽松(大) 
	
	④符号代表
	
	Compact
	
	Any
	
	Regular 
	
	⑤继承性 （尽量少用*组合，这样很可能会导致冲突）
	
	其它8种情况都会继承
	
	会被- - \ + -继承
	
	会被+ - \ + +继承 
	
	⑥sizeclass和autolayout的作用 
	
	sizeclass:仅仅是对屏幕进行了分类（判断屏幕） 
	
	autolayout:对屏幕中各种元素进行约束(位置\尺寸) 
	
	![](/img/iOS基础-AutoLayout自动布局/2.png)
	![](/img/iOS基础-AutoLayout自动布局/3.png)
	![](/img/iOS基础-AutoLayout自动布局/4.png)
	![](/img/iOS基础-AutoLayout自动布局/5.png)
	![](/img/iOS基础-AutoLayout自动布局/6.png)
