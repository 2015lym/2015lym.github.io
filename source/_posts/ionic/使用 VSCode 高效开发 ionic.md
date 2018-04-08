---
layout: pages
title: 使用 VSCode 高效开发 ionic
date: 2017.12.27 22:01
tags: [ionic,工具]
---

![VSCode](http://upload-images.jianshu.io/upload_images/2024647-477e126e6ca50a44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

刚开始开发 ionic 的时候，用的是别的工具。后来发现了 VSCode 这个编辑器，发现相当好用，果断给新接触 ionic 的朋友们安利一波。工具这种东西，只要能提高我们的效率，就果断上。

> 介绍

Visual Studio Code 是微软开发的编辑器，界面简洁漂亮、功能的可扩展性比较强。Ionic的主要编程语言TypeScript 也是由微软开发的，所以在语言支持上也有不少方便之处，而且编辑器相较于 IDE 一般资源占用低、软件占空间较小。

> 安装

[Visual Studio Code](https://code.visualstudio.com)
进官网直接点 Download 即可，同时支持 Mac、Windows、Linux。安装过程不难，不再赘述。编辑器的好处就是

<!-- more -->

> 插件安装

![插件下载](http://upload-images.jianshu.io/upload_images/2024647-41934c67e3d45797.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里是重点，我们点击左侧最下面的按钮，寻找自己想要的插件，点击安装，再点击重新加载即可使用。插件的种类比较多，有代码提示、代码格式化、代码检查等。

搜索后，在插件的详情里可以看到该插件的下载人数，评价星级等。我们直接搜索 ionic，选一些评价较好的进行下载即可。觉得暗色不好看换颜色的话可以使用Ctrl+K、Ctrl+T（Mac上是Command+K、Command+T）进行主题颜色设置，也可以选择安装扩展的主题颜色、扩展的图标等。

![主题色选择](http://upload-images.jianshu.io/upload_images/2024647-811f26dc484541ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 使用技巧

重点中的重点。

1. 使用插件实现 ionic 代码提示
![代码提示](http://upload-images.jianshu.io/upload_images/2024647-185c47dea3134d0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 使用插件实现代码自动审查
ionic 使用的是 TypeScript，在插件中寻找 TSLint 即可。审查规则在一定程度上可以自定义，好的审查可以有效避免一些潜在错误。

3. 自动 import 其他文件
会自动根据文件所在目录 import，不过有时候路径会错，需要手动改。
![自动引入](http://upload-images.jianshu.io/upload_images/2024647-c3b03c5946c841ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 代码格式化
Alt + Shift + F，瞬间让你的代码看起来干净整洁。

5. Control + ` 可以直接在本目录下打开命令行
按下快捷键后，会在底部弹出命令行。加号可以新建更多的命令行，垃圾箱是关闭，叉号是隐藏。所以以后可以不需要新打开一个命令行窗口，直接在里面即可运行 ionic serve。
![命令行](http://upload-images.jianshu.io/upload_images/2024647-17f26f89be2c7190.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



> 目前就是这么多，我如果发现新的技巧会在后面更新。
