title: "Masonry使用总结"

date: 2016-07-21 16:23:43

description:

categories: iOS笔记

tags: [笔记,三方库,iOS]

---

Masonry是AutoLayout的一个第三方类库，也是目前最流行的AutoLayout框架。熟练使用Masonry进行页面布局，能够减少很多不必要的宽高坐标值计算。

<!--more-->

###Masonry的属性

Masonry共提供了11种基本属性，分别是：

	// 左边界
	@property (nonatomic, strong, readonly) MASConstraint *left;
	// 上边界
	@property (nonatomic, strong, readonly) MASConstraint *top;
	// 右边界
	@property (nonatomic, strong, readonly) MASConstraint *right;
	// 下边界
	@property (nonatomic, strong, readonly) MASConstraint *bottom;
	// 首部
	@property (nonatomic, strong, readonly) MASConstraint *leading;
	// 尾部
	@property (nonatomic, strong, readonly) MASConstraint *trailing;
	// 宽度
	@property (nonatomic, strong, readonly) MASConstraint *width;
	// 高度
	@property (nonatomic, strong, readonly) MASConstraint *height;
	// x坐标中心
	@property (nonatomic, strong, readonly) MASConstraint *centerX;
	// y坐标中心
	@property (nonatomic, strong, readonly) MASConstraint *centerY;
	// 文本基线
	@property (nonatomic, strong, readonly) MASConstraint *baseline;
	
除了11种基本属性外，还提供了3种复合属性，用来快速设置控件的frame：
	
	// (top, left, bottom, right)
	@property (nonatomic, strong, readonly) MASConstraint *edges;
	// (width, height)
	@property (nonatomic, strong, readonly) MASConstraint *size;
	// (centerX, centerY)
	@property (nonatomic, strong, readonly) MASConstraint *center;
	
###Masonry的基本方法

熟悉了基本属性之后，我们还需要学习如何去使用这些基本属性去设置控件的约束。同样的，Masonry提供了三种方法来设置控件的约束，我们需要根据不同的情况去使用它。

	// 新增约束
	- (NSArray *)mas_makeConstraints:(void(^)(MASConstraintMaker *make))block;
	// 更新约束（不删除以前的约束）
	- (NSArray *)mas_updateConstraints:(void(^)(MASConstraintMaker *make))block;
	// 重写约束（删除以前的约束）
	- (NSArray *)mas_remakeConstraints:(void(^)(MASConstraintMaker *make))block;

从结果上来看，大部分情况下，使用上面三种方法都能得到我们想要的效果。但他们的功能却是不一样的，从应用性考虑，我们也不能乱用。

`mas_makeConstraints:`：新增约束，该方法只执行一次，适合用于初次设置约束的时候使用，循环调用，会生成许多无谓的约束。

`mas_updateConstraints:`：更新约束，该方法可执行多次，适用于部分约束改变的情况，比如需要根据字体的多少，动态的改变Label的高度。

`mas_remakeConstraints:`：重写约束，该方法会删除原有的约束，避免生成多余的约束，适用于控件的约束改动较大的地方，或代码重复调用的地方，比如cell的复用。

###Masonry使用方法

####常用属性用法

在开始写之前，先将Masonry库文件拖入工程，并包含头文件`Masonry.h`，我们以给一个UIView添加约束为例。

#####offset设置偏移

	// 1.创建UI控件
	UIView *demoView = [[UIView alloc] init];
	demoView.backgroundColor = [UIColor yellowColor];
	// 2.将UI控件加入父视图
	[self.view addSubview:demoView];
	__weak typeof(self) weakSelf = self;
	// 3.创建控件约束
	[demoView mas_makeConstraints:^(MASConstraintMaker *make) {
	    make.left.equalTo(weakSelf.view.mas_left).offset(30);
	    make.right.equalTo(weakSelf.view.mas_right).offset(-30);
	    make.top.equalTo(weakSelf.view.mas_top).offset(30);
	    make.bottom.equalTo(weakSelf.view.mas_bottom).offset(-30);
	}];
	
控件坐标的计算方法如下：

控件的边界 = 参考控件的边界 + 偏移值

效果图：

![](/img/Masonry用法01.png)

#####multipliedBy设置偏移

	// 1.创建UI控件
	UIView *demoView = [[UIView alloc] init];
	demoView.backgroundColor = [UIColor yellowColor];
	// 2.将UI控件加入父视图
	[self.view addSubview:demoView];
	__weak typeof(self) weakSelf = self;
	// 3.创建控件约束
    [demoView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.centerX.equalTo(weakSelf.view.mas_centerX);
        make.centerY.equalTo(weakSelf.view.mas_centerY);
     	make.height.equalTo(weakSelf.view.mas_height).multipliedBy(0.5);
        make.width.equalTo(weakSelf.view.mas_width).multipliedBy(0.5);
    }];
    
控件坐标的计算方法如下：

控件的边界 = 参考控件的边界 * 偏移倍数

效果图：

![](/img/Masonry用法02.png)

####复合属性用法

    UIView *demoView = [[UIView alloc] init];
    demoView.backgroundColor = [UIColor yellowColor];
    [self.view addSubview:demoView];
    __weak typeof(self) weakSelf = self;
    [demoView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(weakSelf.view).insets(UIEdgeInsetsMake(30, 30, 30, 30));
    }];
    
效果图：

![](/img/Masonry用法01.png)

###问题汇总

####动画问题

Masonry动画和我们使用frame来创建动画的方式差不多，需要注意的是，在修改约束后，调用`layoutIfNeeded `方法。

    [UIView animateWithDuration:0.6 animations:^{
        [loginView mas_updateConstraints:^(MASConstraintMaker *make) {
            make.top.equalTo(self.view.mas_top).offset(224.f/667.f*SCREEN_HEIGHT);
        }];
        logoImage.alpha = 1;
        [loginView.superview layoutIfNeeded];//强制绘制
    }];

####frame问题

有时候我们需要根据控件的frame进行一些操作，比如设置控件的圆角，然而在约束设置完成之后，并不能立刻得到它的frame。如果需要立刻得到它的frame，可以调用：

	[view.superview layoutIfNeeded];

	