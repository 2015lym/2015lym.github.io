---
layout: pages
title: ionic 如何使用 Cordova 插件
date: 2018.02.08 21:32
tags: ionic
---

![Cordova](http://upload-images.jianshu.io/upload_images/2024647-3b9d65e3a308ad0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 为什么使用 Cordova

Cordova 提供了与 APP 原生平台交互的功能，使基于网页的APP突破了不能使用设备平台完整功能的限制。

> 如何寻找自己想要的插件

[Cordova 社区](http://cordova.apache.org/plugins/)  
Cordova 社区提供上千个插件，支持各种设备平台的各类硬件特性。

[Github](https://github.com)  
作为最大的程序员同性交友网站，在这里也可以搜索到的很多插件。

[Ionic Native](https://ionicframework.com/docs/native/)  
Ionic 官方提供的插件地址，它与原生 Cordova 插件有所不同，后面会讲到。

<!-- more -->

**选择时需要注意什么：**

1. 选择更新时间更近的插件。时间较为久远的插件，可能会存在方法过时导致不可用。

2. 选择 Star 较多的插件。越多人关注使用的插件，使用起来问题相对较少一些。

> 如何使用 Cordova 插件

由于 Cordova 插件无法使用 Angular 的依赖注入，所以只能通过访问全局变量和方法来调用，十分不方便代码管理。所以 Ionic 开发团队在 Cordova API 基础上封装了一系列开源 Cordova 插件，命名为 **Ionic Native** 的插件集。在我们新建的 Ionic 项目中，打开 package.json 中就可以看到几个自带的 Ionic Native 插件。

接下来我们就分别举例子讲解一下如何使用 `Cordova 原生插件`和 `Ionic Native`。

####Cordova 原生插件

这里举一个获取手机相册的例子，输入以下代码安装 imagepicker 插件

	$ ionic cordova plugin add add cordova-plugin-telerik-imagepicker

之后在需要用到的页面，使用以下方法即可调用手机相册

	showAlbum() {
    this.platform.ready().then(() => {
      (<any>window).imagePicker.getPictures(photo => {
        console.log(photo[0]);
      });
    });

简单分析一下代码：  
1.首先我们使用了 this.platform.ready 方法，这是因为我们需要保证设备已经就绪，否则插件可能会调用失败。  
2.从 (< any>window) 可以看出我们必须使用全局变量来调用，选择图片后，会返回一个数组。  
3.如果只选一张图片的话，直接取[0]即可。取出的数值就是手机的图片路径，可以直接设置 [src] 进行显示。

![图片路径](http://upload-images.jianshu.io/upload_images/2024647-f2bd73a53ef3ff53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####Ionic Native

Ionic Native 与原生 Cordova 插件不同，它的安装需要进行两步。以 ActionSheet 插件举例

	$ ionic cordova plugin add cordova-plugin-actionsheet
	$ npm install --save @ionic-native/action-sheet
	
第一个步骤和之前安装插件完全相同，第二步则是安装 Ionic 封装的代码。所以不需要的时候我们也要输入两条指令才能彻底删除。

	$ ionic cordova plugin rm cordova-plugin-actionsheet
	$ npm uninstall --save @ionic-native/action-sheet
	
Ionic Native 的使用方法，官方文档写的已经很简单了，我简单举一个例子。

    showActionSheet() {
      let buttonLabels = ['你好', '确定'];
      const options: ActionSheetOptions = {
        title: '你好Ionic',
        buttonLabels: buttonLabels,
        addCancelButtonWithLabel: '取消',
        androidTheme: this.actionSheet.ANDROID_THEMES.THEME_HOLO_LIGHT,
        destructiveButtonLast: true
      };
      this.actionSheet.show(options);
    }

![ActionSheet](http://upload-images.jianshu.io/upload_images/2024647-d06ca9d994eddf76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####插件的限制

寻找好用的插件十分有助于开发的工作，但并不是所有插件都可以在所有平台使用，也不是所有方法都可以使用。在 [Ionic Native](https://ionicframework.com/docs/native/) 寻找插件时，首先要看支持的平台。

![支持的平台](http://upload-images.jianshu.io/upload_images/2024647-35eb020b2fd3bac3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

之后也不是所有方法都可以使用，可能作者也没在介绍的地方标出来。比如 [Keyboard](https://ionicframework.com/docs/native/keyboard/) 这个插件，其中有一个方法 `show()` ，我在查看源码时发现在 iOS 中是不可用的。

![Keyboard](http://upload-images.jianshu.io/upload_images/2024647-8828d8f651dde4aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以在日常开发时，使用插件如果遇到问题了，不妨寻找 Android、iOS 开发协助查看源码，调查一下为什么失败。
