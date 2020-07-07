title: "CALayer-CAReplicatorLayer"

date: 2018-07-06 18:43:12

description: 

categories: iOS开发 - 基础

tags: [iOS,CALayer]

---

基于产品需求，在进行某些动画开发中，不可避免的会遇到需要创建重复图层的情况。直接循环创建看起来似乎有些蠢。

CALayer提供了`CAReplicatorLayer`子类，很好的解决了这个问题，它可以将自己的子图层复制指定的次数，并且保留被复制图层的各种基础属性。

<!--more-->

#### 常用属性

* **instanceCount**

	复制次数，针对其所有的子图层，默认值是1(不复制)。
	
* **instanceDelay**

	复制延迟，一般配合其他动画使用。
	
* **instanceTransform**

	复制图层相对于上一个被复制图层的位移。
	
#### 使用示例

```swift

UIView *loadingView = [self createLoadingView:CGRectMake(100, 100, 30, 30)];
[self.view addSubview:loadingView];
loadingView.center = self.view.center;

- (UIView *)createLoadingView:(CGRect)frame {
    UIView *loadingView = [[UIView alloc] init];
    loadingView.frame = frame;
    CAReplicatorLayer *replicatorLayer = [CAReplicatorLayer layer];
    replicatorLayer.frame = loadingView.bounds;
    [loadingView.layer addSublayer:replicatorLayer];
    
    CALayer *dot = [CALayer layer];
    dot.frame = CGRectMake(0, 0, 6, 6);
    dot.backgroundColor = [UIColor blackColor].CGColor;
    dot.cornerRadius = 3;
    dot.masksToBounds = YES;
    [replicatorLayer addSublayer:dot];
    
    CGFloat count = 8.0;
    replicatorLayer.instanceCount = count;
    CGFloat angel = 2 * M_PI/count;
    replicatorLayer.instanceTransform = CATransform3DMakeRotation(angel, 0, 0, 1);
    
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
    animation.duration = 1;
    animation.fromValue = @1;
    animation.toValue = @0.1;
    animation.repeatCount = MAXFLOAT;
    animation.removedOnCompletion = NO;
    animation.fillMode = kCAFillModeForwards;
    [dot addAnimation:animation forKey:nil];
    replicatorLayer.instanceDelay = 1.0/ count;
    dot.transform = CATransform3DMakeScale(0.01, 0.01, 0.01);
    return loadingView;
}

```

**效果图**

![效果图](/img/iOS基础-CALayer-CAReplicatorLayer/79752-50b154bd6305d70a.gif)
