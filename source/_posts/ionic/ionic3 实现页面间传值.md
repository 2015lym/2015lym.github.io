---
layout: pages
title: ionic3 实现页面间传值
date: 2018.01.04 10:44
tags: ionic
---

在 ionic 中，页面跳转一般有两种形式 ModalController 和 NavController。接下来分别介绍一下这两种跳转如何正向传值和反向传值。

### ModalController
ModalController 通常用于选择或编辑某项目。它不可以被重复使用，并且当他dismiss后会被彻底销毁。

<!-- more -->

> 正向传值

首先在第一个页面引入 ModalController

	import { ModalController } from 'ionic-angular';
	
	constructor(public modalCtrl: ModalController) {}

	
然后在跳转页面的地方使用以下方法

    let data: Object = {
      username: '张三'
    };
    let profileModal = this.modalCtrl.create(ModPage, data);
    profileModal.present();

来到第二个页面。引入 NavParams 和 ViewController

	import { NavParams, ViewController } from 'ionic-angular';
	
	constructor(public navParams: NavParams, 
				public viewCtrl: ViewController) {}


接下来使用 NavParams 获取上一个页面传递的值。成功的话应该可以在页面上看到`用户名：张三`

    ionViewDidLoad() {
      let modelData: string = '用户名：' + this.navParams.get('username');
      console.log(modelData);
    }
    
在需要关闭页面时，调用以下方法即可

	this.viewCtrl.dismiss();
	
> 反向传值

ModalController 实现反向传值比较简单，首先改造一下 第二个页面的 dismiss() 方法

	let data: Object = { info: '返回的消息' };
    this.viewCtrl.dismiss(data);

然后再修改一下第一个页面的 create() 方法即可，成功的话可以看到控制台输出`返回的消息`

    let data: Object = {
      username: '张三'
    };
    let profileModal = this.modalCtrl.create(ModPage, data);
    profileModal.onDidDismiss(data => {
      console.log(data.info);
    });
    profileModal.present();

### NavController
NavController 是最基本的页面跳转方式。基本的使用方法就是通过push进入新的页面，pop回到上一个页面。我们push的时候，堆栈中就加入了一个新的界面，pop的时候会把最后的一个页面移除以返回上一个页面。

> 正向传值

首先在第一个页面引入 NavController

	import { NavController } from 'ionic-angular';
	
	constructor(public navCtrl: NavController) {}
	
然后在跳转页面的地方使用以下方法

    let data: Object = {
      username: '张三'
    };
    this.navCtrl.push(NavPage, data);
    

来到第二个页面。引入 NavParams 和 NavController

	import { NavParams } from 'ionic-angular';
	
	constructor(public navParams: NavParams) {}
				
与 ModalController 一样，使用 NavParams 获取上一个页面传递的值。成功的话应该可以在页面上看到`用户名：张三`

    ionViewDidLoad() {
      let modelData: string = '用户名：' + this.navParams.get('username');
      console.log(modelData);
    }

在需要关闭页面时，不需要做更多操作。因为这种方法跳转的页面，ionic 会自动在左上角生成返回按钮。如果需要自己控制返回，使用以下方法即可

	this.navCtrl.pop();
	
> 反向传值

按照产品设计来讲，需要反向传值一般会使用 ModalController。所以 NavController 并没有提供可以直接使用的方法，需要我们自己实现。实现方法有很多，这里我简单介绍一种

首先来到第一个页面，将跳转的方法进行以下修改

    let data: Object = {
      username: '张三',
      callback: data => {
        console.log(data.info);
      }
    };
    this.navCtrl.push(NavPage, data);

然后来到第一个页面，手动制作一个返回按钮，并使用以下方法作为 click 事件

    popData() {
      let callback = this.navParams.get('callback');
      let data: Object = {
        info: '返回的消息'
      };
      callback(data);
      this.navCtrl.pop();
    }
    
成功的话可以看到控制台输出`返回的消息`
