title: "封装自己的ActionSheet控件"

date: 2016-08-09 17:37:21

description:

categories: iOS开发 - 项目

tags: [iOS,ActionSheet,控件]

---

在开发中，弹出框是必不可少的一个控件，苹果也有自带UIAlertController来供我们使用，通常情况下，使用系统的弹出框就可以了。

但是，需求总是游离在现实之外的，万恶的产品和UI可能会要求你改这改那，这个字体太丑了，这个颜色不好看。哈哈，没办法，改吧。

但是系统自带的弹出框，样式就那么几种，颜色和字体也不能随意更改，为了适应需求，我们需要封装一个自己的ActionSheet控件。

以下是效果图：

![](/img/iOS项目-封装自己的ActionSheet控件/封装自己的ActionSheet控件.gif)

字体和颜色都预留了接口，可以随意更改。

Github下载地址：

[https://github.com/MrWheat/MZActionSheetDemo](https://github.com/MrWheat/MZActionSheetDemo)
