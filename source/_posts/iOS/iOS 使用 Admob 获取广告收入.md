---
layout: pages
title: iOS 使用 Admob 获取广告收入
date: 2017.06.23 14:36
tags: [iOS,工具]
---

![](http://upload-images.jianshu.io/upload_images/2024647-ee741bebb0a1606c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.配置项目
---
- 进入[AdMob官网](https://www.admob.com)，注册个账号。  
- 然后点击添加应用：

<!-- more -->

![添加应用.png](http://upload-images.jianshu.io/upload_images/2024647-4a692c5854c6a0f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个有效每千次展示费用，就是你的广告被人看1000次你会得到的收入。如图展示了1000次，你就会得到**10.95美元**。

>添加完成后，进入应用概览，点击添加广告单元：

![添加广告单元.png](http://upload-images.jianshu.io/upload_images/2024647-aeffb2772b70d2dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>过程中我们可以对广告进行配置：

![样式配置1.png](http://upload-images.jianshu.io/upload_images/2024647-f6b3cf74294ce92d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>原生广告的话，还可以对样式进行调整。

![样式配置2.png](http://upload-images.jianshu.io/upload_images/2024647-b3ddf05e41c0af43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.SDK下载和集成
---
非常简单，到下面这个地址下载后直接拖入项目即可，想用CocoaPods也可以，不过感觉没必要。  
[AdMob iOS SDK下载](https://developers.google.com/admob/ios/download?hl=zh-CN)

3.如何在项目中展示
---
下面以横幅广告作为例子

进入你需要插入广告的页面	
		
	//引入头文件
	#import <GoogleMobileAds/GoogleMobileAds.h>
		
	//新建一个属性
	@property (nonatomic, strong) GADBannerView *bView;
		
	_bView = [[GADBannerView alloc] initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, 50)];
	//这个就是你添加的广告单元的ID
	_bView.adUnitID = @"广告ID";
	_bView.rootViewController = self;
	GADRequest *request = [GADRequest request];
	
	//如果是开发阶段，需要填写测试手机的UUID，不填写可能会误会你自己刷展示量
	request.testDevices = @[@"测试设备的UUID"];
	    
	[_bView loadRequest:request];
	[self.view addSubView:_bView];
	
运行项目即可看到:

![广告.png](http://upload-images.jianshu.io/upload_images/2024647-ef20e1b7f84dd48f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其他的原生广告、视频广告，大家可以在官方文档自行学习，这里就举一个例子。

常见问题：
---
- **千次展示费用是怎么算的。**  
Google自动计算的，我最多的时候是千次44$，少的时候是3$。

- **测试的时候是否有广告收入。**
有。

- **为什么按照你的方法设置了，但是广告不显示。**
  - 新添加的广告单元需要过几个小时才能使用
  - 你填写了错误的ID


- **我可不可以多个应用共享一个广告ID。**  
完全可以，不过这样你就无法统计哪个App的观看量更多了。

- **有没有其他广告样式。**
有横幅、原生、视频等。

- **是不是用户必须点击了广告才有收益。**  
不是，展示了就会有。点击的另算。很多游戏都使用观看视频广告给予奖励的激励方式诱导玩家，嘿嘿。

- **怎么提现。**  
等你收入累积到100$以上，才可以填写收款卡号等信息进行提现。

- **有没有相关的分析、统计服务。**  
[Firebase](https://firebase.google.com/docs/admob/admob-firebase)

>好了就是这么多了，喜欢的可以点个赞，有不明白的就在下方留言吧。
