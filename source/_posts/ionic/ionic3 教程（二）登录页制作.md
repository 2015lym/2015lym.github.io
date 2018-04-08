---
layout: pages
title: ionic3 教程（二）登录页制作
date: 2017.05.10 14:08
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-9165b8f221ced2f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

链接：  
[ionic3 教程（一）安装和配置](http://www.jianshu.com/p/1baf40713c1c)  
[ionic3 教程（二）登录页制作](http://www.jianshu.com/p/0f024a62ba14)  
[ionic3 教程（三）设置页制作](http://www.jianshu.com/p/7ea502ef2e49)  
[ionic3 教程（四）安卓硬件返回键处理](http://www.jianshu.com/p/02f190059aaa)  
[ionic3 教程（五）基本的网络请求](http://www.jianshu.com/p/3ad54d7d1077)

`内容已跟进到 ionic3.5.0 最新版本`

运行 platform 中 iOS 和 Android 项目，会发现一行警告，Not found www/index.html。

这是因为我们的上一次测试的页面是根目录中 src 的内容，需要把 src 路径下的内容同步过来。

    // iOS
    $ ionic cordova build ios
    // Android
    $ ionic cordova build android

    // 执行完毕后可以去以下路径检查一下是否有html文件了
    
    // iOS
    platform/ios/www
    // Android
    platform/android/assets/www

有了内容之后，就可以在 Xcode 和 Android Studio 上运行项目了。

<!-- more -->

这里直我们做一个登录页面，了解一些基本知识。

1.修改 Tabs，让目录结构清晰
---
打开 Tabs.html，修改为如下代码：
	
	<ion-tabs>
		<ion-tab *ngFor="let tabRoot of tabRoots" [root]="tabRoot.root" tabTitle="{{tabRoot.tabTitle}}" tabIcon="{{tabRoot.tabIcon}}"></ion-tab>
	</ion-tabs>

打开 Tabs.ts，修改为如下代码：

	import { Component } from '@angular/core';

	import { AboutPage } from '../about/about';
	import { ContactPage } from '../contact/contact';
	import { HomePage } from '../home/home';
	
	@Component({
	  templateUrl: 'tabs.html'
	})
	export class TabsPage {
	  tabRoots: Object[];
	
	  constructor() {
	    this.tabRoots = [
	      {
	        root: HomePage,
	        tabTitle: 'Home',
	        tabIcon: 'home'
	      },
	      {
	        root: ContactPage,
	        tabTitle: 'Contact',
	        tabIcon: 'notifications'
	      },
	      {
	        root: AboutPage,
	        tabTitle: 'About',
	        tabIcon: 'document'
	      }
	    ];
	  }
	}

这样你的目录结构就更加清晰了，方便以后为 Tabs 增加新的模块

2.新建登录页
---
    // cd到项目目录，然后执行下面的代码
    $ ionic g page login
	
该命令会自动生成这几个文件，如图

![login.png](http://upload-images.jianshu.io/upload_images/2024647-2ce0eee295c129d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.打开 app.module.ts
---
导入新生成的 Login

	import { LoginPage } from '../pages/login/login';
	
并分别在加入 declarations 和 entryComponents 后面加入 LoginPage

我们所有新建的页面都需要在 module 进行声明才能使用

4.打开app.component.ts
---
	import { LoginPage } from '../pages/login/login';
	
import 登录文件，并修改 rootPage 为 LoginPage

5.打开login.html文件，写入以下代码
---
    <ion-header>
      <ion-navbar>
        <ion-title>登录</ion-title>
      </ion-navbar>
    </ion-header>

    <ion-content>
        <ion-item>
          <ion-label fixed>账号</ion-label>
          <ion-input type="text" placeholder="请输入账号" #username></ion-input>
        </ion-item>
        <ion-item>
          <ion-label fixed>密码</ion-label>
          <ion-input type="password" placeholder="请输入密码" #password></ion-input>
        </ion-item>
      <ion-item no-lines>
        <label item-right>记住密码</label>
        <ion-toggle></ion-toggle>
      </ion-item>

      <div style="text-align: center; margin-left: 30px; margin-right: 30px;">
        <button ion-button block color="danger" (click)="logIn(username, password)">
          登录
        </button>
      </div>
    </ion-content>
	
效果如图所示，其中给 ion-item 加上 no-lines 可以去除底部的线条。（运行方式上一节已经讲过了）

![登录.png](http://upload-images.jianshu.io/upload_images/2024647-30606b90d854997a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

界面的样式会自动适配系统，这里安卓的标题会自动左对齐，解决的方法是在scss文件加入

    .toolbar-title-md {
      text-align: center;
    }

再举个例子，toggle 在安卓上会显示：  

![Android.png](http://upload-images.jianshu.io/upload_images/2024647-f002ac7e94e50ead.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
iOS会显示：

![iOS.png](http://upload-images.jianshu.io/upload_images/2024647-43db0cc9dccb11da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

颜色什么的当然可以改，相关的可以查阅官方文档。

仔细体会每一行代码，可以试着删掉一些元素来看看会发生什么改变。有人私聊问过我按钮怎么居中啊，怎么改变按钮宽度，这里直接上代码。

6.登录的实现
---
修改button的代码为：

	<button ion-button block color="danger" (click)="logIn()">
	
并在login.ts中写下以下代码：
	
	logIn() {
		alert('登录');
	}

浏览器自动 build 之后，再次点击，就会弹出登录的提示框。这个logIn方法没有写权限修饰符，默认的话是 `public` 的。

这里有个问题，我们如何传值和验证。这里我给出一种简单的解决方案。这个 (click)="logIn()" 明显后面的括号里是可以传值的。我们修改代码如下

login.html  
分别修改两个 input

	<ion-input type="text" placeholder="请输入账号" #username></ion-input>
	<ion-input type="password" placeholder="请输入密码" #password></ion-input>

将值传入 button 的点击事件

	<button ion-button block color="danger" (click)="logIn(username, password)">
	
login.ts  
	
修改logIn方法

	logIn(username: HTMLInputElement, password: HTMLInputElement) {
		let userinfo: string = '用户名：' + username.value  + '密码：' + password.value;
		alert(userinfo);
	}
	
这回你点击时候就会输出用户名和密码了。
同理，我们可以加上`验证`
	
	logIn(username: HTMLInputElement, password: HTMLInputElement) {
		if (username.value.length == 0) {
		    alert("请输入账号");
		} else if (password.value.length == 0) {
		    alert("请输入密码");
		} else {
		    let userinfo: string = '用户名：' + username.value + '密码：' + password.value;
		    alert(userinfo);
		}
	}
	
最后导入头文件，并实现跳转

	import { TabsPage } from "../tabs/tabs";
	
在登录成功后面加入以下代码：
	
	this.navCtrl.push(TabsPage);
	
测试一下，输出账号密码，点击登录即可跳转到 Tabs 页面。

>Demo[下载地址](https://github.com/2015lym/ionic3Demo/tree/demo2)
