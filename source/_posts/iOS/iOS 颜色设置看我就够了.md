---
layout: pages
title: iOS 颜色设置看我就够了
date: 2017.07.16 16:56
tags: iOS
---

![](http://upload-images.jianshu.io/upload_images/2024647-fdf516583f619bf8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最近发现做界面的时候，有时会忘记某种控件的颜色怎么设置，需要去网上进行搜索，所以写下这篇文章。  

一方面是收藏起来自己查阅，一方面是分享给大家。目标是有了这篇文章，不用再去搜索和颜色设置有关的内容。  
下面进入正题

<!-- more -->

导航栏
---
    /* 全局设置 */

    // 标题颜色
    // 如果需要设置字体就在字典中加入 [UIFont fontWithName:@"Hiragino Sans GB" size:14]
    [[UINavigationBar appearance] setTitleTextAttributes:
						@{NSForegroundColorAttributeName:[UIColor whiteColor]}];

    // 导航栏背景颜色
    [[UINavigationBar appearance] setBarTintColor:[UIColor whiteColor]];

    // 导航栏返回按钮、自定义UIBarButtonItem颜色
    [[UINavigationBar appearance] setTintColor:[UIColor blackColor]];


    /* 单独设置 */

    // 导航栏标题颜色
    self.navigationController.navigationBar.titleTextAttributes = @{NSForegroundColorAttributeName:[UIColor whiteColor]};

    // 导航栏背景颜色
    self.navigationController.navigationBar.barTintColor = [UIColor whiteColor];

    // 导航栏返回按钮、自定义UIBarButtonItem颜色
    self.navigationController.navigationBar.tintColor = [UIColor blackColor];


状态栏
---
进入 **Targets -> General -> Status Bar Style**，可以设置 黑色(默认) 和 白色。
![状态栏.png](http://upload-images.jianshu.io/upload_images/2024647-7888dcb45ecb2b70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>如果需要精确控制不同页面的颜色，还是需要代码设置。

首先给 `info.plist` 加上这句话
![](http://upload-images.jianshu.io/upload_images/2024647-bccb5327f1fd879a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    // View controller-based status bar appearance
    // 加入这个参数，我们前面方法的设置就会失效
    // 接下来就可以使用代码进行设置了

    /* 全局设置 */

    [UIApplication sharedApplication].statusBarStyle = UIStatusBarStyleLightContent;

    /* 单独设置 */

    - (UIStatusBarStyle)preferredStatusBarStyle {
        return UIStatusBarStyleLightContent;
    }
    
    // 细心的朋友读者可能会疑问，为什么这次不能用
    self.navigationController.preferredStatusBarStyle = UIStatusBarStyleLightContent;

![](http://upload-images.jianshu.io/upload_images/2024647-a2b52ccb0e359919.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

答案很简单，仔细看报错就知道这是一个 `readonly` 的属性，所有我们直接**重写他的 set 方法。**

TabBar
---
    /* 全局设置 */
    // TabBar背景颜色
    [UITabBar appearance].barTintColor = [UIColor whiteColor];

    /* 单独设置 */
    // TabBar背景颜色
    self.tabBarController.tabBar.barTintColor = [UIColor whiteColor];

>TabBar图标颜色
不用写乱七八糟的代码，直接到 Assets.xcassets 里把图片的属性 Render 设置为 Original Image 就可以让颜色按照图片的来，而不会选中变蓝了。

![TabBar图标颜色](http://upload-images.jianshu.io/upload_images/2024647-380ab7a533417150.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Button
---
    // 字体颜色
    // 有人可能会误用这两个错误的方法
    // 错误1：[button.titleLabel setTextColor:[UIColorblackColor]];
    // 错误2：button.titleLabel.textColor = [UIColor redColor];
    // 正确
    [button setTitleColor:[UIColor blackColor]
                 forState:UIControlStateNormal];

    // 边框颜色
    // 默认没有边框，第一行是设置线条，第二行重点在于layer的颜色要用CGColor
    button.layer.borderWidth = 2.0;
    button.layer.borderColor = [UIColor blackColor].CGColor;

TextField
---
    // placeholder颜色设置
    textField.attributedPlaceholder = [[NSAttributedString alloc] initWithString:@"placeHoldtext" attributes:@{NSForegroundColorAttributeName: [UIColor redColor]}];    
AttributedString
---
    // 初始化NSMutableAttributedString
    NSMutableAttributedString *str = [[NSMutableAttributedString alloc] initWithString:@"Using NSAttributed String"];
    // 颜色设置
    [str addAttribute:NSForegroundColorAttributeName
                value:[UIColor blueColor]
                range:NSMakeRange(0,5)];
    [str addAttribute:NSForegroundColorAttributeName
                value:[UIColor redColor]
                range:NSMakeRange(6,12)];
    [str addAttribute:NSForegroundColorAttributeName
                value:[UIColor greenColor]
                range:NSMakeRange(19,6)];
    // 字体设置
    [str addAttribute:NSFontAttributeName
                value:[UIFont fontWithName:@"Arial-BoldItalicMT" size:30.0]
                range:NSMakeRange(0, 5)];
    [str addAttribute:NSFontAttributeName
                value:[UIFont fontWithName:@"HelveticaNeue-Bold" size:30.0]
                range:NSMakeRange(6, 12)];
    [str addAttribute:NSFontAttributeName
                value:[UIFont fontWithName:@"Courier-BoldOblique" size:30.0]
                range:NSMakeRange(19, 6)];
    // 把AttributedString赋值给Label
    attrLabel.attributedText = str;

通用部分
---

    // 字体颜色    适用于Label、TextField、TextView等
    label.textColor = [UIColor whiteColor];
    textField.textColor = [UIColor yellowColor];
    textView.textColor = [UIColor yellowColor];

    // 背景颜色    基本都使用
    someView.backgroundColor = [UIColor whiteColor];

工具
---
>系统自带的测色工具，位置在 应用程序 -> 实用工具( Launchpad 里叫其他) -> 数码测色计

![数码测色计](http://upload-images.jianshu.io/upload_images/2024647-aaaf5ba72cdaac8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**使用方法：**
`打开后指向你想测色的地方即可显示他的 RGB 色，以这个 Switch 举个例子。`

![颜色测试](http://upload-images.jianshu.io/upload_images/2024647-75933c18f922f29f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们设置完rgb色后和你想要的略有差别。这里提供一个解决办法。**设置颜色的时候，点击右边的小齿轮，选择 sRGB。**

![sRGB](http://upload-images.jianshu.io/upload_images/2024647-0f5ddb49ef25393a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>几种常用的列举的差不多了。不完整的地方大家可以提出来，我会对这个文章进行更新。
