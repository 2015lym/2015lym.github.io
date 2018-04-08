---
layout: pages
title: ionic3 教程（一）安装和配置
date: 2017.05.10 10:42
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-2fa0ad1be7f4d995.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

链接：  
[ionic3 教程（一）安装和配置](http://www.jianshu.com/p/1baf40713c1c)  
[ionic3 教程（二）登录页制作](http://www.jianshu.com/p/0f024a62ba14)  
[ionic3 教程（三）设置页制作](http://www.jianshu.com/p/7ea502ef2e49)  
[ionic3 教程（四）安卓硬件返回键处理](http://www.jianshu.com/p/02f190059aaa)  
[ionic3 教程（五）基本的网络请求](http://www.jianshu.com/p/3ad54d7d1077)

`内容已跟进到 ionic3.5.0 最新版本`

学习本教程前你需要了解以下内容：
- 前端基本知识（HTML5、JavaScript、CSS）
- [TypeScript](https://www.tslang.cn/docs/handbook/generics.html)
- [Angular](https://angularjs.org)

TypeScript的话至少需要了解基本语法，可以看看  
[TS 中文官方文档](https://www.tslang.cn/docs/home.html)

对 Angular 不太熟悉的可以点这里：  
[Angular 4.0 架构详解](http://www.jianshu.com/p/3c06260e6015)  
[Angular 4.0 内置指令全攻略](http://www.jianshu.com/p/4cc3a04ca83a)

<!-- more -->

什么是 ionic？他和 Cordova、Angular2 有什么联系？  
---
`ionic = Cordova + Angular + ionic CSS`  
Cordova 提供了使用 JavaScript 调用 Native 功能，ionic CSS 是一套 UI 框架，ionic 对 Angular 进行了封装。

ionic3
---
ionic 基于 AngularJS 开发。由于 AngularJS1.x 版本在性能上已经很难有较大提升，Google 推出了全新设计的 Angular 2，ionic 也对应着出了2。

ionic2 与一代相比有较大的变化，基于最新的 Angular 2 ，使用 TypeScript 进行开发，如果您没有接触过 AngularJS 或 Ionic1.x，完全不用担心，直接从 ionic2 开始学习即可。

ionic3 是 Angular4.0 推出之后的跟进版本，变化幅度不大。

安装和运行
---
如果没有安装过 `Node.js`，先去[官网](https://nodejs.org/en/)下载一下。

    // 安装（失败的话 Mac 尝试使用 sudo，Windows 尝试管理员身份运行 cmd）
    $ npm install -g cordova ionic
    // 安装后可以验证一下 ionic cli 版本
    $ ionic -version
    3.5.0

    // 创建应用
    // cd 到要创建项目的目录，输入以下内容创建ionic项目
    // ionic3Demo 是项目名，tabs是模板（默认是tabs，其他还有blank的单页等）
    $ ionic start ionic3Demo tabs

    // 安装依赖
    // 会生成一个 node_modules 文件夹，并在里面安装 package.json 上写下的文件
    $ cd ionic3Demo/
    $ npm install

    // 在浏览器中运行项目
    $ ionic serve

> 有的朋友可能对 cli 不太熟悉，简单说两句。
-g 代表全局安装、install 可以简写为 i、-version 可以简写为 -v
剩下的还有 --save、--dev 等，想要了解更多可以参考官方文档
http://ionicframework.com/docs/cli/

如图：

![ionic serve](http://upload-images.jianshu.io/upload_images/2024647-cb069caa83da89d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在web上运行 iOS、Android、WindowsPhone 项目

    把浏览器中的地址改为：
    http://localhost:8100/ionic-lab


![ionic serve lab](http://upload-images.jianshu.io/upload_images/2024647-0aed6ceff8cf53c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不喜欢使用命令行的朋友，可以试一试 ionic lab，他是桌面版的开发环境。不过我没有尝试过，感兴趣的可以下载看看。
`下载地址：`http://lab.ionic.io
![ionic lab](http://upload-images.jianshu.io/upload_images/2024647-3a8133d6dd67094d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    // 如果需要在手机运行，则执行以下命令
    // 添加iOS项目
    $ ionic platform add ios
    // 把src里的内容同步到ios项目中(src后面会讲到)
    $ ionic cordova build ios
    // 运行iOS项目,相当于在Xcode里面按Command+R
    $ ionic cordova emulate ios
	
    //安卓的话同理，把ios替换为android即可
    $ ionic platform add android
    $ ionic cordova build android
    $ ionic cordova emulate android
成功后目录结构如下，platform 里面是你添加的 iOS 和 Android 项目。

![目录结构](http://upload-images.jianshu.io/upload_images/2024647-df4903357fa4727e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**src** 就是以后开发写代码主要的地方。每次写完要同步到手机项目中，需要使用

    $ ionic cordova build ios
    $ ionic platform add android

同步到 iOS 和 Android 平台里。

Tabs 分析
---
打开 `app.component.ts` 可以看到这句代码

    rootPage:any = TabsPage;
这个相当于 `iOS 的 rootViewController` 和 `Android 的 MainActivity`。  

回忆一下我们一开始选择的 Tabs 模板，所以这里生成的是 tabs。打开 `src/pages/tabs/tabs.ts`，可以看到这些代码：

    import { Component } from '@angular/core';

    import { AboutPage } from '../about/about';
    import { ContactPage } from '../contact/contact';
    import { HomePage } from '../home/home';

    @Component({
      templateUrl: 'tabs.html'
    })
    export class TabsPage {

      tab1Root = HomePage;
      tab2Root = AboutPage;
      tab3Root = ContactPage;

      constructor() {

      }
    }

这个 TabsPage，就代表了应用最下面的三个 TabBar。上面分别指出了他们的路径。

Ionic 3 的导航和 iOS 一样是一个栈，push 到新界面，pop 回旧界面。我们在构造函数中设置了 `this.navCtrl` 属性，我们可以调用 `this.navCtrl.push()` 方法，来导航到一个新的页面。

好了，这一节内容就到这里。

补充
---
最近有很多朋友私信问我，如何升级 ionic 版本和 ionic cli 版本，也搞不清楚这两个东西的区别和联系，这里统一进行解释。

**ionic 版本**是 package.json 中的 ionic-angular。  

    // 升级方法：
    // 修改 ionic-angular 的版本号，并在项目目录下执行以下命令
    npm install 

**ionic cli 版本**是一套 ionic 的命令行界面，用于执行各种命令行的命令。  

    // 升级方法：
    // 在任意目录下，执行以下命令
    npm install -g ionic@lastest
