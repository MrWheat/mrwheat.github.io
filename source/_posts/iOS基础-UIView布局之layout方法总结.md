title: "UIView布局之layout方法总结"

date: 2016-07-05 15:42:23

description:

categories: iOS开发 - 基础

tags: [笔记,Layout,iOS]

---

最近接触了用到constraint的动画，写的时候遇到了一点问题，于是整理研究了一些关于UIView布局的layout方法，这里分享给大家，对于控件的约束，这里用到的是目前比较流行的Masonry库。

<!--more-->

下面列举了一些常见的与UIView的layout相关的方法。

* layoutSubviews

* layoutIfNeeded

* setNeedsLayout

* setNeedsDisplay

* drawRect

* sizeThatFits

* sizeToFit

### layoutSubviews

#### 相关介绍

	- (void)layoutSubviews; 

layoutSubviews方法默认是不做任何事情的，但是在iOS6以后，当UIView上存在约束条件时，它会使用这些约束去决定subviews的position和size。而当UIView的子类需要对其subviews进行更精确的布局时，则可以重写此方法，在此方法中直接设置subviews的frame。

通过该方法可以对subviews重新布局，更新子视图，但是不能直接调用此方法，如果你想强制更新布局，你可以调用setNeedsLayout方法；如果你想立即数显你的views，你需要调用layoutIfNeeded方法。

#### 被调用的情况

苹果官方文档强调，不能直接调用layoutSubviews对子视图进行重新布局，layoutSubviews只有在以下情况下才会被调用。

* 调用layoutIfNeeded时。
* addSubview的时候。
* 当view的frame发生改变的时候。
* 滑动UIScrollView的时候。
* 旋转Screen会触发父UIView上的layoutSubviews事件。
* 改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件。

### setNeedsLayout

	- (void)setNeedsLayout;

它的作用是将控件标记为“需要刷新”，然后异步调用layoutIfNeeded重新布局，不立即刷新，在系统runloop的下一个周期自动调用layoutSubviews。

如果更改了控件上的约束，会自动调用setNeedsLayout方法，将其标记为“需要刷新”。

### layoutIfNeeded

	- (void)layoutIfNeeded;

立即刷新，调用该方法，如果有“需要刷新”的标记，会立即调用layoutSubviews进行布局，反之则不会。

因此如果要立即刷新，首先需要调用setNeedsLayout方法将控件标记为“需要刷新”，然后调用layoutIfNeeded方法重新布局。当然，在视图第一次显示之前，标记总是“需要刷新”的，可以直接调用layoutIfNeeded。

### 代码说明

	- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField {
	    self.editing = textField;
	    [UIView animateWithDuration:0.6 animations:^{
	        [self.loginView mas_updateConstraints:^(MASConstraintMaker *make) {
	            make.top.equalTo(self.view.mas_top).offset(154.f/667.f*SCREEN_HEIGHT);
	        }]; // 更改约束
	        self.logoImage.alpha = 0;
	        [self.loginView.superview layoutIfNeeded];//立即刷新
	    }];
	    return YES;
	}

以上是通过Masonry库更新约束条件写的一个小动画，它的执行过程如下。

首先通过Masonry库更新self.loginView的约束，默认会调用setNeedsLayout方法，并将其标记为“需要刷新”。

然后通过父控件self.loginView.superview调用layoutIfNeeded，立即刷新父控件里的所有子控件，对子控件重新布局。

### drawRect

使用drawRect方法来执行重绘的任务。

#### 被调用的情况

* 如果初始化时设置了View的大小，则会调用。

* 该方法在调用sizeToFit后会被自动调用。

* 通过设置contentMode属性值为UIViewContentModeRedraw，那么将在每次设置或更改frame的时候自动调用drawRect方法。

* 直接调用setNeedsDisplay，或者setNeedsDisplayInRect时。

#### 使用注意情况

* 若使用UIView绘图，只能在drawRect方法中获取相应的contextRef并绘图。

* drawRect方法不能直接调用，必须通过调用setNeedsDisplay或者 setNeedsDisplayInRect，让系统自动调用该方法。

* 若使用calayer绘图，只能在drawInContext方法中绘制，同样也是调用setNeedDisplay。

* 若要实时画图，不能使用gestureRecognizer，只能使用touchesBegan等方法来掉用setNeedsDisplay实时刷新屏幕。

### sizeToFit

使用sizeToFit需要注意以下几点：

* sizeToFit会自动调用sizeThatFits方法。

* sizeToFit不应该在子类中被重写，应该重写sizeThatFits。

* sizeThatFits传入的参数是receiver当前的size，返回一个适合的size。





