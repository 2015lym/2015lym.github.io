---
layout: pages
title: ionic3 教程（四）安卓硬件返回键处理
date: 2017.06.28 15:48
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-c7d62f76b2eaa639.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

链接：  
[ionic3 教程（一）安装和配置](http://www.jianshu.com/p/1baf40713c1c)  
[ionic3 教程（二）登录页制作](http://www.jianshu.com/p/0f024a62ba14)  
[ionic3 教程（三）设置页制作](http://www.jianshu.com/p/7ea502ef2e49)  
[ionic3 教程（四）安卓硬件返回键处理](http://www.jianshu.com/p/02f190059aaa)  
[ionic3 教程（五）基本的网络请求](http://www.jianshu.com/p/3ad54d7d1077)

前几天测出了一个问题，就是在根目录点击安卓的硬件返回键，会直接导致程序退出。在网上找到了 `小军617` 写的这篇[ionic2实战-完美处理安卓硬件返回按钮](http://www.jianshu.com/p/6aa5a8318092)。

这篇文章解决了一部分问题，可以让我们在 Tabs 下点击返回按钮弹出一个 toast 提示“再按一次退出应用”，然后如果用户在2秒内再按一次就会真的退出。不过Demo 在登录页点击返回按钮没有任何反应，而且点击退出登录后，点击硬件返回按钮会回到主界面。

所以我打算对这个方法进行一下优化，包括以下几点。

- 将方法从 app.component.ts 中抽出做成一个公共方法，便于其他页面使用
- 修复退出登录后，点击硬件返回按钮会回到主界面
- 在 tabs 页，如果不在首页标签，点击硬件返回按钮，先跳转到首页。之后再按两次退出

<!-- more -->

1.在 src 目录下，新建文件夹 services 和文件 backButton.service.ts
---
输入以下代码，我已经写了很全的注释，仔细看
	
	import { Injectable } from '@angular/core';
	import { Platform, ToastController, App, NavController, Tabs } from 'ionic-angular';
	
	@Injectable()
	export class BackButtonService {
	
	  //控制硬件返回按钮是否触发，默认false
	  backButtonPressed: boolean = false;
	
	  //构造函数 依赖注入
	  constructor(public platform: Platform,
	              public appCtrl: App,
	              public toastCtrl: ToastController) { }
	
	  //注册方法
	  registerBackButtonAction(tabRef: Tabs): void {
	    
	    //registerBackButtonAction是系统自带的方法
	    this.platform.registerBackButtonAction(() => {
	      //获取NavController
	      let activeNav: NavController = this.appCtrl.getActiveNav();
	      //如果可以返回上一页，则执行pop
	      if (activeNav.canGoBack()) {
	        activeNav.pop();
	      } else {
	        if (tabRef == null || tabRef._selectHistory[tabRef._selectHistory.length - 1] === tabRef.getByIndex(0).id) {
	          //执行退出
	          this.showExit();
	        } else {
	          //选择首页第一个的标签
	          tabRef.select(0);
	        }
	      }
	    });
	  }
	
	  //退出应用方法
	  private showExit(): void {
	    //如果为true，退出
	    if (this.backButtonPressed) {
	      this.platform.exitApp();
	    } else {
	        //第一次按，弹出Toast
	        this.toastCtrl.create({
	            message: '再按一次退出应用',
	            duration: 2000,
	            position: 'top'
	        }).present();
	      //标记为true
	      this.backButtonPressed = true;
	      //两秒后标记为false，如果退出的话，就不会执行了
	      setTimeout(() => this.backButtonPressed = false, 2000);
	    }
	  }
	}

2.修改 app.module.ts
---
导入 BackButtonService，并添加到 providers 里

3.修改 Tabs
---
Tabs.html

	把<ion-tabs>修改为<ion-tabs #myTabs>
	
Tabs.ts

这是修改了的部分，可以看看都改动了哪些

	引入ViewChild、Platform、Tabs、BackButtonService	
	@ViewChild('myTabs') tabRef: Tabs;

	platform.ready().then(() => {
	  this.backButtonService.registerBackButtonAction(this.tabRef);
	});

完整版

	import { Component, ViewChild } from '@angular/core';
	import { Platform, Tabs } from 'ionic-angular';
	
	import { HomePage } from '../home/home';
	import { SettingPage } from "../setting/setting";
	import { BackButtonService } from "../../services/backButton.service";
	
	@Component({
	  templateUrl: 'tabs.html'
	})
	export class TabsPage {
	  tabRoots: Object[];
	  @ViewChild('myTabs') tabRef: Tabs;
	
	  constructor(public backButtonService: BackButtonService,
	              private platform: Platform) {
	    this.tabRoots = [
	      {
	        root: HomePage,
	        tabTitle: 'Home',
	        tabIcon: 'home'
	      },
	      {
	        root: SettingPage,
	        tabTitle: '设置',
	        tabIcon: 'person'
	      }
	    ];
	
	      platform.ready().then(() => {
	          this.backButtonService.registerBackButtonAction(this.tabRef);
	      });
	  }
	}

>platform.ready().then 主要是为了保证能注册上，因为如果不是 ready 状态有可能会失败

4.Login
---
Login.ts

这是修改了的部分，可以看看都改动了哪些

	引入Platform、BackButtonService	

	platform.ready().then(() => {
	  this.backButtonService.registerBackButtonAction(null);
	});
	
完整版

	import { Component } from '@angular/core';
	import { IonicPage, ModalController} from 'ionic-angular';
	import { TabsPage } from "../tabs/tabs";
	import { Platform } from 'ionic-angular';
	import { BackButtonService } from "../../services/backButton.service";
	
	@IonicPage()
	@Component({
	  selector: 'page-login',
	  templateUrl: 'login.html',
	})
	export class LoginPage {
	
	  constructor(public modalCtrl: ModalController,
	              private backButtonService: BackButtonService,
	              private platform: Platform) {
	      platform.ready().then(() => {
	          this.backButtonService.registerBackButtonAction(null);
	      });
	  }
	
	  ionViewDidLoad() {
	    console.log('ionViewDidLoad LoginPage');
	  }
	
	  logIn(username: HTMLInputElement, password: HTMLInputElement) {
	    if (username.value.length == 0) {
	      alert("请输入账号");
	    } else if (password.value.length == 0) {
	      alert("请输入密码");
	    } else {
	      let userinfo: string = '用户名：' + username.value + '密码：' + password.value;
	      alert(userinfo);
	      let modal = this.modalCtrl.create(TabsPage);
	      modal.present();
	    }
	  }
	
	}

>这里的 registerBackButtonAction 传了一个 null 进去，回去看看 backButton.service.ts 的判断，我们就可以知道，这里是为了让他能执行双击退出操作

5.使用安卓手机测试
---

	//执行这两句
	$ ionic cordova platform add android
	$ ionic cordova build android
	
接下来就可以运行了，我这边是直接打开 Android Studio 装到手机运行的，亲测有效。试一试吧


>Demo[下载地址](https://github.com/2015lym/ionic3Demo/tree/demo4)
