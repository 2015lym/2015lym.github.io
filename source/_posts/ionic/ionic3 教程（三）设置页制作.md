---
layout: pages
title: ionic3 教程（三）设置页制作
date: 2017.06.25 22:26
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-d34550bb3489088d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

链接：  
[ionic3 教程（一）安装和配置](http://www.jianshu.com/p/1baf40713c1c)  
[ionic3 教程（二）登录页制作](http://www.jianshu.com/p/0f024a62ba14)  
[ionic3 教程（三）设置页制作](http://www.jianshu.com/p/7ea502ef2e49)  
[ionic3 教程（四）安卓硬件返回键处理](http://www.jianshu.com/p/02f190059aaa)  
[ionic3 教程（五）基本的网络请求](http://www.jianshu.com/p/3ad54d7d1077)

`内容已跟进到 ionic3.5.0 最新版本`

现在大部分 App，都会有自己的设置页（个人信息页），里面会包含个人资料、设置、退出登录等功能。这一回我们就在制作一个个人信息页。

1.删除 ContactPage 和 AboutPage
---
首先删除文件，然后删除 app.module.ts 中的引用，最后删除 Tabs.ts 中的引用。如果删除后运行不了就再检查一下或者重新运行一下，你做项目的时候迟早会遇到需要删文件的情况。

<!-- more -->

2.添加设置页面
---
- 添加设置页

    ionic g page Setting

- 在app.module.ts中import，并分别写到declarations和entryComponents中。

- 在 tabs.ts 中引入，并在 tabRoots 中插入这段代码

      {
        root: SettingPage,
        tabTitle: '设置',
        tabIcon: 'person'
      }

Title 是标题，这里的 tabIcon 用的是 ionic 提供的默认图标，图标大全请看 
https://ionicframework.com/docs/ionicons/  （最新）
http://ionicons.com/  （已过时）

![icon.png](http://upload-images.jianshu.io/upload_images/2024647-1bb4db7267cf5968.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用时直接输入左边的 Name 值即可。

3.制作设置页内容
---
打开 setting.html，输入以下代码
~~~
    <ion-header>
        <ion-navbar>
            <ion-title>
                更多
            </ion-title>
        </ion-navbar>
    </ion-header>

    <ion-content class="bg-color">
        <ion-list class="top-list">
            <button ion-item [navPush]="userInfoPage">
                <ion-avatar item-left>
                    [图片上传失败...(image-c93b50-1513351069560)]
                </ion-avatar>
                <h2>devilx</h2>
                <p>账号：18600001111</p>
            </button>
        </ion-list>

        <ion-list>
            <ion-item>
                控件1
            </ion-item>
            <button ion-item>
                控件2
            </button>
            <button ion-item>
                <ion-avatar item-left>
                    [图片上传失败...(image-55ea8-1513351069560)]
                </ion-avatar>
                控件3
            </button>
        </ion-list>

        <ion-list>
            <button ion-item>
                版本号
                <span  item-end>V1.0</span>
            </button>
            <button ion-item>
                关于
            </button>
        </ion-list>

        <div style="text-align: center; margin-left: 30px; margin-right: 30px;margin-top: 30px;">
            <button ion-button block (click)="logOut()">
                退出登录
            </button>
        </div>
    </ion-content>
~~~
打开setting.scss，输入以下代码

	page-setting {
	  .toolbar-title-md{
	    text-align: center;
	  }
	
	  .scroll-content{
	    overflow: hidden;
	  }
	
	  .bg-color{
	    background-color: #efeeee;
	  }
	
	  .top-list{
	    margin-top: 15px;
	  }
	}

运行效果如图所示（我这里使用了 http://localhost:8100/ionic-lab，所以展示的是 iOS 的效果）：

![设置页.png](http://upload-images.jianshu.io/upload_images/2024647-52cee0cc6d783e8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这一段我多展示了几种效果，代码自行理解加深以下印象。

4.制作退出登录功能
---
首先我们需要回到 login.ts，把跳转方式改一下，因为如果使用 push 的方法进行跳转，即使你隐藏了返回按钮，安卓手机也是可以使用`返回键`跳回上一个页面的。所以我们要使用另一种跳转方式

进入 login.ts，引入 ModalController，删除无用的引用，如下

	import { Component } from '@angular/core';
	import { IonicPage, ModalController} from 'ionic-angular';
	import { TabsPage } from "../tabs/tabs";
	
	@IonicPage()
	@Component({
	  selector: 'page-login',
	  templateUrl: 'login.html',
	})
	export class LoginPage {
	
	  constructor(public modalCtrl: ModalController) {
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

然后到 Setting.ts 页面，改为以下代码

	import { Component } from '@angular/core';
	import { IonicPage, ModalController } from 'ionic-angular';
	import { LoginPage } from "../login/login";
	
	@IonicPage()
	@Component({
	  selector: 'page-setting',
	  templateUrl: 'setting.html',
	})
	export class SettingPage {
	
	  constructor(public modalCtrl: ModalController) {
	  }
	
	
	  ionViewDidLoad() {
	    console.log('ionViewDidLoad SettingPage');
	  }
	
	  logOut() {
	    let modal = this.modalCtrl.create(LoginPage);
	    modal.present();
	  }
	
	}

OK，退出登录功能制作完成，这一节到此结束。

>Demo[下载地址](https://github.com/2015lym/ionic3Demo/tree/demo3)
