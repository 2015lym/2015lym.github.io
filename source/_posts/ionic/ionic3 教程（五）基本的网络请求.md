---
layout: pages
title: ionic3 教程（五）基本的网络请求
date: 2017.08.03 22:30
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-3b0538c8de8be43d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

链接：  
[ionic3 教程（一）安装和配置](http://www.jianshu.com/p/1baf40713c1c)  
[ionic3 教程（二）登录页制作](http://www.jianshu.com/p/0f024a62ba14)  
[ionic3 教程（三）设置页制作](http://www.jianshu.com/p/7ea502ef2e49)  
[ionic3 教程（四）安卓硬件返回键处理](http://www.jianshu.com/p/02f190059aaa)  
[ionic3 教程（五）基本的网络请求](http://www.jianshu.com/p/3ad54d7d1077)

这是最后一节，本节主要用最简单网络请求和基本的内置指令做一个演示。

- 对 Angular 的基本架构不熟悉的请点[Angular 4.0 架构详解](http://www.jianshu.com/p/3c06260e6015)  
- 对 Angular 的内置指令不熟悉的请点[Angular 4.0 内置指令全攻略](http://www.jianshu.com/p/4cc3a04ca83a)

前言
---
通常我们希望在 HTTP 请求的时候，页面不会失去响应，所以我们的 HTTP 请求是异步的。  
JavaScript 中，通常用 3 种方式处理异步代码。

1. 回调（callback）
2. 承诺（promise）
3. 可观察对象（observable）

> promise 和 observable 主要三个主要不同：
- observable 可以中途取消，promise 发出就不行
- observable 可以持续发射很多值，而 promise 只能发射一个值就结束了
- observable 提供了更多的工具函数，最常用的是 filter 等

<!-- more -->

想更多了解 promise 的请看
 [《JavaScript Promise迷你书》](http://liubin.org/promises-book/)

想更多了解  rxjs(observable) 的请看
[《rxjs中文教程》](https://www.gitbook.com/book/buctwbzs/rxjs/details)

`在 Angular 中，处理异步代码的最佳方式就是使用可观察对象，所以接下来会用到`

app.module.ts
---

导入 Angular 的 HttpModule。

    import { HttpModule } from '@angular/http';
	
    //然后在 imports 中插入 HttpModule 即可
	
测试用接口
---

本来找了一些别的接口作为测试，发现不会显示数据，打开 Chrome 测试了一下发现有**跨域**问题。

    XMLHttpRequest cannot load

所以这里给大家推荐个网站，我们就用他来进行测试。
http://jsonplaceholder.typicode.com/

拖到下面的 Resources 可以看到图片。从图中可以看出接口种类还是比较丰富的，我们选择带图片的 /photos 进行测试。

![Resources](http://upload-images.jianshu.io/upload_images/2024647-3b44c3720627d95a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


home.ts
---

    import { Http, Response } from '@angular/http';
	
    @Component({
      selector: 'page-home',
      templateUrl: 'home.html'
    })
    export class HomePage {
      // 接收数据用
      listData: Object;

      // 依赖注入
      constructor(public navCtrl: NavController, private http: Http) {

      }

      ionViewDidLoad() {
        // 网络请求
        this.http.request('http://jsonplaceholder.typicode.com/photos')
        .subscribe((res: Response) => {
          this.listData = res.json();
        });
      }

http.request 会返回一个 Observable 对象。我们可以使用 subscribe 订阅变化。

当 http.request 从服务器返回一个流时，它就会发出一个 Response 对象。我们用 json 方法提取出响应体解析成一个 Object，最后将它赋值给 this.listData。

home.html
---

    <ion-header>
      <ion-navbar>
        <ion-title>首页</ion-title>
      </ion-navbar>
    </ion-header>

    <ion-content padding>
      <ion-list *ngFor="let item of listData">
        <ion-item>
          <ion-avatar item-left>
            <img [src]="item?.url">
          </ion-avatar>
          {{item?.title}}
        </ion-item>
      </ion-list>
    </ion-content>

这里使用了一个 ngFor 遍历了 listData，生成了一个列表数据。还有一点要提一下，这个 `item?.title` 是 Angular 的一种语法，如果对象为空就不会取值，可以防止报错。

最后效果如图所示
![效果图](http://upload-images.jianshu.io/upload_images/2024647-c90ce20dea63e129.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再补上一个 Promise 的写法

    import 'rxjs/add/operator/toPromise';

    this.http.request('http://jsonplaceholder.typicode.com/photos')
      .toPromise()
      .then(res => { this.listData = res.json(); })
      .catch(err => { console.error(err) });

自己动手试一试吧。这个入门系列结束之后，应该会去找一些模块进行深入点的展开。

>Demo[下载地址](https://github.com/2015lym/ionic3Demo/tree/demo5)
