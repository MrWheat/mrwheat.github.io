title: "UITableView上取消UITextField第一响应者的问题"

date: 2016-07-04 17:24:59

description:

categories: iOS开发 - 问题

tags: [UITextField,iOS,技巧]

---

使用系统自带键盘时，不可避免的经常需要我们人为的去收起键盘。类似点击空白或者输入框以外的地方将键盘收起，因为键盘的存在不仅影响界面美观，同时也因为我们需要在该界面进行其它操作，而键盘会遮挡部分视图，使这些操作进行的不是那么方便。而收起键盘的问题，本质上就是取消第一响应者的问题。

<!--more-->

### 取消第一响应者的方法

系统提供了两种方法来取消控件的第一响应者。

* \- (BOOL)endEditing:(BOOL)force;    

	 该方法用来强制取消某个控件和其所有的子控件的第一响应者。
	 
	 使用方式如`[self.view endEditing:YES];`
	
* \- (BOOL)resignFirstResponder;

	该方法则是用来单独取消某个控件的第一响应者。
	
	使用方式如`[self.view resignFirstResponder];`

### 点击ViewController空白部分收起键盘

解决点击控制器空白部分收起键盘应该是我们最常见到的需求之一了。解决办法也很简单，在控制器的`touchesBegan`方法里，调用`endEditing`或`resignFirstResponder`方法即可搞定。

	- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
	    [super touchesBegan:touches withEvent:event];
	    [self.view endEditing:YES];
	}

### 点击其他控件收起键盘

当然也有可能遇到一些其他需求，类似点击图片或者按钮什么的，将键盘收起。这个就需要分别在控件的点击事件里调用`endEditing`方法或`resignFirstResponder`方法来实现。

	// UIButton的点击事件
	- (void)buttonClick:(UIButton *)sender {
	    [self.view endEditing:YES];
	}
	
	// UIImageView的手势
	- (void)tapClick:(UIGestureRecognizer *)tap {
    	[self.view endEditing:YES];
	}
	
### 点击UITableView收起键盘

UITableView作为开发中最常见的控件之一，当然也经常会遇到需要键盘收起的问题。相较于其他控件来说，UITableView比较尴尬。因为该控件分为很多个部分，表头、表尾、组头、组尾以及表中的Cell，我们不可能分别去处理UITableView各个部分的点击事件，然后在点击事件里取消第一响应者。根据UITableView里控件结构的复杂程度，这是一个不可预估的工作量。

如果只是在cell的点击事件中取消第一响应者的身份，那还勉强能接受，只需要在`didSelectRowAtIndexPath`代理方法中调用`endEditing`方法即可。

	- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
	    [self.view endEditing:YES];
	}
	
如果情况稍微复杂一点就不行了，比如点击TableView下面的空白部分，或者点击组头组尾的时候取消第一响应者。

然后有人开始需找一劳永逸的方法，尝试在UITableView上添加点击手势。

	- (void)createTableView {
	    self.tableView = [[MZTableView alloc] initWithFrame:CGRectMake(0, 100, self.view.frame.size.width, 400)];
	    self.tableView.delegate = self;
	    self.tableView.dataSource = self;
	    UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tapClick:)];
	    [self.tableView addGestureRecognizer:tap];
	    [self.view addSubview:self.tableView];
	}
	
	- (void)tapClick:(UITapGestureRecognizer *)tap {
    	[self.view endEditing:YES];
	}
	
这样能够解决问题，但又会迎来新的问题，你会发现点击cell的时候，`didSelectRowAtIndexPath`这个代理方法不执行了，原因是你新加的手势覆盖了TableView里面自带的手势，当然如果你不需要用到`didSelectRowAtIndexPath`代理方法的话，倒是可以这么做。

正确的做法应该是通过查找事件源，在事件响应之前，取消UITextField的第一响应者的身份，具体步骤是新建一个UITableView的继承类，然后在该类中重写如下方法。

	- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
	    id view = [super hitTest:point withEvent:event];
	    if (![view isKindOfClass:[UITextField class]]) {
	        [self endEditing:YES];
	    }
	    return view;
	}
	

