---
layout: pages
title: iOS 去除项目中的警告
date: 2017.01.15 19:52
tags: iOS
---

![](http://upload-images.jianshu.io/upload_images/2024647-566d44e3f61e1933.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>普通的自己就能修复的警告就不讲了，主要写一些特殊的警告，帮助大家治疗一下强迫症。

`注：有补充或者错误的请留言`

去除Pod文件中的警告
---
    在Podfile文件中加入
    inhibit_all_warnings!

<!-- more -->

注释出现警告
---
    Xcode->Preferences->Source->Enable Source Control 勾勾去掉   
![图片.png](http://upload-images.jianshu.io/upload_images/2024647-a572f4153741ea50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

file missing
---
    解决办法：
              1. 终端cd到指定路径   
              2. svn rm filepath/filename
    删错了恢复：svn revert -R .
`如果用的是git，把svn替换成git即可`

The Copy Bundle Resources build phase contains this target's Info.plist file 'Info.plist'.
---
    在Target的Build Phases中移除即可

directory not found for option '-L/…
---
    在Target的Build Settings中移除即可
