---
layout: pages
title: ionic3 命令行报错解决方法大全（持续更新）
date: 2017.12.27 22:01
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-887c02ca1fae0644.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###本文档会不断更新推荐收藏，有帮助可以点个喜欢

目录：
---
1. cordova 7.0 版本 build ios 失败  
2. ios-deploy 报错  
3. cordova-hot-code-push-cli 安装报 permission denied  
4. You need to be logged into your Ionic account in order to run ionic cordova resources.  
5. 官方账号登录报 404  
6. ionic cordova build ios/android 后没有样式
7. build 失败 The product name change ( tag) in config.xml
8. ionic cordova build android 报错  
9. ionic cordova platform add ios/android 一直 Running command  
10. 执行 ionic cordova 开头的命令都自动执行 ionic integrations enable cordova 并报错
11. ionic cordova build ios/android Error: spawn EACCES
12. 新建项目报错 Network connectivity error occurred, are you offline?

<!-- more -->

**`一些命令如果执行失败，Windows 下可尝试使用管理员身份运行，Mac 下可尝试试着在命令前加 sudo，`**
**`一些命令如果执行失败，Windows 下可尝试使用管理员身份运行，Mac 下可尝试试着在命令前加 sudo，`**
**`一些命令如果执行失败，Windows 下可尝试使用管理员身份运行，Mac 下可尝试试着在命令前加 sudo，`**

正文：
---
> **问题1**：Cordova 7.0 版本 build ios 失败  
> Error: Cannot find module '../plugman/platforms/ios'

**解决方法：**

	1.cordova降级
	sudo npm i -g cordova@6.5.0
	
	2.进入路径 plugins/cordova-hot-code-push-plugin/scripts/lib
	  找到文件 iosWKWebViewEngineSupport.js
	  用以下代码替换原方法
	  
	function loadProjectFile() {
	  var platform_ios;
	  var projectFile;
	  try {
	    // try pre-5.0 cordova structure
	    platform_ios = context.requireCordovaModule('cordova-lib/src/plugman/platforms')['ios'];
	    projectFile = platform_ios.parseProjectFile(iosPlatformPath());
	  } catch (e) {
	    try {
	      // let's try cordova 5.0 structure
	      platform_ios = context.requireCordovaModule('cordova-lib/src/plugman/platforms/ios');
	      projectFile = platform_ios.parse(iosPlatformPath());
	    } catch (e) {
	      // try cordova 7.0 structure
	      var iosPlatformApi = require(path.join(iosPlatformPath(), '/cordova/Api'));
	      var projectFileApi = require(path.join(iosPlatformPath(), '/cordova/lib/projectFile.js'));
	      var locations = (new iosPlatformApi()).locations;
	      projectFile = projectFileApi.parse(locations);
	    }
	  }
	  return projectFile;
	}

参考资料：  
https://github.com/ionic-team/ionic-plugin-deeplinks/issues/99

**补充**

    // 如果使用方法2报以下错误的话
    CHCP plugin after prepare hook:
    Error: iosPlatformPath is not a function

    就把方法2内所有 iosPlatformPath() 的 '()' 删除

> **问题2**：build ios 失败  
> npm ERR! This is most likely a problem with the ios-deploy package

**解决方法：**
	
	npm install -g ios-deploy --unsafe-perm=true
	
	// 如果还是报错，试试更新 npm
	// 这两天提示更新 1.9.2，怎么都升级不了，后来更新了 npm 直接成功
	npm install -g npm
	
参考资料：  
https://stackoverflow.com/questions/42213400/npm-ios-deploy-install-issues

> **问题3**：npm install -g cordova-hot-code-push-cli 
> Error: permission denied

**解决方法：**

	Mac 下试试加 sudo
	
	还是失败就安装 Python2.7
	brew install python
	我用的是 Homebrew，自己想办法安装

> **问题4**：[WARN] You need to be logged into your Ionic account in order to run ionic cordova resources.

解决方法：
	
	https://dashboard.ionicjs.com/signup
	去官网申请 ionic 账号解决
	
	// 其实不登录也没事，只是用不了下面这个命令而已
	ionic cordova resources

> **问题5**：  
> ? Email:xxx  
> ? Password: [hidden]  
> Request: POST https://api.ionic.io/login  
> Response: 404

解决方法：
	
	如果你非要注册账号，登录可能会遇到这个问题
	输入以下指令：
	npm install -g ionic@latest 
	ionic config set backend pro -g

参考资料：  
https://stackoverflow.com/questions/45885432/ionic-cant-login-request-post-https-api-ionic-io-login-no-user-found-by-that

> **问题6**：ionic cordova build ios/android 后没有样式

解决方法：

	删除本地node_modules，重新 npm install

> **问题7**：Error：The product name change (<name> tag) in config.xml is not supported dynamically....

解决方法：

	这个问题是已经生成了项目后，又改了项目名，build 就会报这个错。
	重新生成即可
	ionic cordova platform rm ios/android
	ionic cordova platform add ios/android

> **问题8**：ionic cordova build android 报错
> ANDROID_HOME=C:\Users\dell\AppData\Local\Android\Sdk
> JAVA_HOME=C:\Program Files\Java\jdk1.8.0_31
> Error: Could not find an installed version of Gradle either in Android Studio...

解决方法：

	检查安卓环境配置、SDK 是否下了，最后再重新生成一下 android 项目
	ionic cordova platform rm android
	ionic cordova platform add android
	
> **问题9**：  
> ionic cordova platform add ios/android 一直 Running command

解决方法：

	网络问题，请科学上网

> **问题10**：  
> 执行 ionic cordova 开头的命令都自动执行 ionic integrations enable cordova 

内容：

	> ionic integrations enable cordova
	...

解决方法：

	// 在项目目录下输入
	ionic config set integrations.cordova.enabled false
	
	// 执行后会自动在 ionic.config.json 插入以下内容
	// 再执行各种命令就不会报错了
        // 需要注意的是，禁止该项可能会导致一些指令失效

    "integrations": {
      "cordova": {
        "enabled": false
      }
    }

> **问题11**：  
> ionic cordova build ios/android Error: spawn EACCES

解决方法：

	sudo chmod -R 777 /Applications/Android\ Studio.app/Contents/gradle/gradle-4.1/bin/gradle

之前我一直是没有问题的，自从升级了 **`Android Studio 3.0`** 后产生的，我使用了参考资料第一个网址最后一个答案解决了问题。

参考资料：  
https://forum.ionicframework.com/t/ionic-v3-7-0-error-spawn-eacces-on-android-build/101436/6  
https://stackoverflow.com/questions/38767126/getting-error-spawn-eacces-while-ionic-build-android-in-ubuntu-14-04/44431678#44431678

> **问题12**：  
> 新建项目报错 Network connectivity error occurred, are you offline?

报错内容：

	[ERROR] Network connectivity error occurred, are you offline?
	
	If you are behind a firewall and need to configure proxy settings, see:
	https://ionicframework.com/docs/cli/configuring.html#using-a-proxy

解决方法：

	1.安装 @ionic/cli-plugin-proxy
	// npm install -g @ionic/cli-plugin-proxy 
	2.如果仍未解决，请尝试更新最新的 npm
	// npm install -g npm
	3.如果仍未解决，请尝试将 DNS 改为 8.8.8.8
	4.如果仍未解决，请尝试科学上网
