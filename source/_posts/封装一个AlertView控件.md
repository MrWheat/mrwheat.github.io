title: "封装一个AlertView控件"

date: 2016-08-10 17:03:07

description:

categories: 项目开发

tags: [iOS,AlertView,控件]

---

系统的UIAlertView和UIActionSheet在iOS9.0已经被苹果废弃，由UIAlertController代替。

然而UIAlertController的样式只有有限的几种，对于特殊的字体和颜色需求，UIAlertController并没有预留接口给我们。

当然虽然修改的办法还是有的，但一不小心触及到苹果的底线，影响产品上线，也会增加时间成本。

之前封装了一个ActionSheet底部弹出视图，这里再封装一个AlertView提示框，常用的弹出框基本就这两种了。

以下是效果图：

![](/img/封装一个AlertView控件.gif)

Github下载地址：

[https://github.com/MrWheat/MZAlertViewDemo](https://github.com/MrWheat/MZAlertViewDemo)