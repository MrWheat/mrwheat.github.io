title: "常用iOS系统应用API"

date: 2016-08-02 14:32:43

description:

categories: iOS笔记

tags: [图片,UIImage,iOS]

---

前面讲沙盒目录的时候说过，iOS应用都是被一个小箱子锁起来的独立个体，里面的数据出不来，外面的数据进不去，除了某些特殊的情况。[博客传送门](http://www.hackmz.com/2016/07/25/沙盒目录和应用程序包/)

我们在开发App的时候可以预留一些Api用来与其他应用进行交互，而对于系统应用来说，比如地图，相册，照相机这些预装应用，苹果也为我们提供了一些工具来访问它们。

那怎么做呢？

**PS:**以下代码大部分均需在真机上调试才有效果。

<!--more-->

###照片

使用照片应用，一般用来取照片或者保存照片，以下介绍这两种方法的实现。

* 取出照片

	1. 首先遵守协议

			#import "ViewController.h"
			@interface ViewController () <UIImagePickerControllerDelegate,UINavigationControllerDelegate>
			@end

	2. 打开相册
	
		    if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]) {
		        NSLog(@"没有相册权限，请到设置中打开");
		        return;
		    }
		    UIImagePickerController *ipc = [[UIImagePickerController alloc] init];
		    ipc.sourceType = UIImagePickerControllerSourceTypePhotoLibrary;
		    ipc.delegate = self;
		    [self presentViewController:ipc animated:YES completion:nil];
		    
	3. 实现协议方法

			- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info {
			    [picker dismissViewControllerAnimated:YES completion:nil];
			    //  1.取出选中的图片
			    UIImage *image = info[UIImagePickerControllerOriginalImage];
			} 
			
* 保存图片

	1. 保存图片

			UIImage *image = [UIImage imageNamed:@"testImage.jpg"];
			UIImageWriteToSavedPhotosAlbum(image, self, @selector(image:didFinishSavingWithError:contextInfo:), nil);
			
	2. 实现保存图片的方法（不需要遵守协议）

			- (void)image:(UIImage *)image didFinishSavingWithError:(NSError *)error contextInfo:(void *)contextInfo {
			    if(error != NULL){
			        NSLog(@"保存图片失败");
			    }else{
			        NSLog(@"保存图片成功");
			    }
			}
	
###相机

有些App经常需要应用相机拍照的功能，该功能的使用和照片类似。

1. 首先遵守协议

		#import "ViewController.h"
		@interface ViewController () <UIImagePickerControllerDelegate,UINavigationControllerDelegate>
		@end

2. 打开相机

	    if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) {
	        NSLog(@"没有相机权限，请到设置中打开");
	        return;
	    }
	    UIImagePickerController *ipc = [[UIImagePickerController alloc] init];
	    ipc.sourceType = UIImagePickerControllerSourceTypeCamera;
	    ipc.cameraFlashMode = UIImagePickerControllerCameraFlashModeAuto;
	    ipc.delegate = self;
	    [self presentViewController:ipc animated:YES completion:nil];
	    
3. 实现协议方法

		- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info {
		    [picker dismissViewControllerAnimated:YES completion:nil];
		    //  1.取出选中的图片
		    UIImage *image = info[UIImagePickerControllerOriginalImage];
		}

###打电话

打电话有3种方式，推荐第三种方式。

1. 直接跳转拨号界面拨打电话

	    NSString *num = [[NSString alloc] initWithFormat:@"tel://%@", @"10086"];
	    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:num]]; 
	    
2. 拨号之前弹窗询问（私有Api，可能无法过审）

	NSURL *url = [NSURL URLWithString:@"telprompt://10086"];
	[[UIApplication sharedApplication] openURL:url];
	
3. 创建一个UIWebView来加载URL，拨完能自动回到原应用

		UIWebView *webView = [[UIWebView alloc]init];
		NSURL *url = [NSURL URLWithString:@"tel://10086"];
		[webView loadRequest:[NSURLRequest requestWithURL:url ]];

###发送短信

发送短信有两种方式。

* 直接跳转发短信界面发送短信

		NSURL *url = [NSURL URLWithString:@"sms://10086"];
		[[UIApplication sharedApplication] openURL:url];
		
* 使用MessageUI框架发送短信

	1. 首先包含头文件，然后遵守协议
	
			#import <MessageUI/MessageUI.h>
			@interface ViewController () <MFMessageComposeViewControllerDelegate>
			@end
			
	2. 编写短信内容，设置收件人
	
		    // 判断是否允许发短信
		    if ([MFMessageComposeViewController canSendText]) {
		        MFMessageComposeViewController *vc = [[MFMessageComposeViewController alloc]init];
		        //设置短信内容
		        vc.body = @"吃饭了没";
		        //设置收件人列表
		        vc.recipients = @[@"10010",@"10086"];
		        //设置代理
		        vc.messageComposeDelegate = self;
		        //显示控制器
		        [self presentViewController:vc animated:YES completion:nil];
		    }
	
	3. 实现协议方法
			
			- (void)messageComposeViewController:(MFMessageComposeViewController *)controller didFinishWithResult:(MessageComposeResult)result {
			    NSLog(@"发送完成");
			}

	
		
###发送邮件

发送邮件的方式和短信差不多，同样也有两种。

* 跳转邮件客户端

		NSURL *url = [NSURL URLWithString:@"mailto://maizi@qq.com"];
		[[UIApplication sharedApplication] openURL:url];
		
* 使用MessageUI框架发送邮件

	1. 首先包含头文件，然后遵守协议
	
			#import <MessageUI/MessageUI.h>
			@interface ViewController () <MFMailComposeViewControllerDelegate>
			@end
			
	2. 编写邮件内容，设置收件人
	
		    // 判断是否允许发邮件
		    if ([MFMailComposeViewController canSendMail]) {
		        MFMailComposeViewController *mvc = [[MFMailComposeViewController alloc] init];
		        //设置邮件主题
		        [mvc setSubject:@"测试会议"];
		        //设置邮件内容
		        [mvc setMessageBody:@"开会" isHTML:NO];
		        //设置收件人列表
		        [mvc setToRecipients:@[@"test@qq.com"]];
		        //设置抄送人列表
		        [mvc setCcRecipients:@[@"test1@qq.com"]];
		        mvc.mailComposeDelegate = self;
		        [self presentViewController:mvc animated:YES completion:nil];
		    }
	
	3. 实现协议方法
			
			- (void)mailComposeController:(MFMailComposeViewController *)controller didFinishWithResult:(MFMailComposeResult)result error:(nullable NSError *)error {
				if (error) {
			    	NSLog(@"发送失败");
				} else {
			    	NSLog(@"发送成功");
				}
			}

###Safari打开网址

通过Safari打开网址只需要调用openURL:方法即可。

	[[UIApplication sharedApplication] openURL:[NSURL URLWithString:url]];

###打开App Store

1. 跳转AppStore首页

		NSString *str = [NSString stringWithFormat:
		                     @"itms-apps://itunes.apple.com"];
		[[UIApplication sharedApplication] openURL:[NSURL URLWithString:str]];

2. 找到应用程序的描述链接，然后将`http://`替换为`itms://`或者`itms-apps://:`。或者在iTunes Connect中找到应用程序id进行拼接。

		NSString *str = [NSString stringWithFormat:@"itms-apps://itunes.apple.com/app/id%@",appleID];  
		[[UIApplication sharedApplication] openURL:[NSURL URLWithString:str]]; 
		