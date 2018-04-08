---
layout: pages
title: Chrome 网页调试技巧
date: 2017.08.22 21:22
tags: 工具
---

![Chrome](http://upload-images.jianshu.io/upload_images/2024647-375472a2bf0e8b6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

网上有个笑话，说 Chrome 是披着浏览器皮的操作系统。虽然只是一句玩笑话，但是也反映了 Chrome 功能较多的事实。

**这篇文章的主题是 Chrome 如何调试网页，ionic 的也适用。**因为 Chrome 功能非常多，我仅把**最常用的功能**列出来进行分享。文章结尾有彩蛋。

准备材料
---
**Chrome 浏览器**  
[http://www.google.cn/chrome/browser/desktop/](http://www.google.cn/chrome/browser/desktop/)

这个地址是官方的，应该没有被墙。不要再去乱七八糟的网站下了。

正文
---
首先拿[百度](https://www.baidu.com)举例。

运行 Chrome，打开百度首页。右键检查可打开调试面板，快捷键如下：

功能/平台  | Windows | Mac
----    |------    |---- 
打开/关闭 DevTools | F12、Ctrl + Shift + I  | Command + Option + I  
打开 DevTools | Ctrl + Shift + C  | Command + Option + C  

<!-- more -->

> 查看鼠标指向的元素

就是鼠标指哪显示哪里的元素，右侧按钮是开关
![](http://upload-images.jianshu.io/upload_images/2024647-7844aa43571e3181.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 手机模式

可以模拟手机的运行，左侧按钮可以切换手机型号，右侧按钮可以 打开/关闭 手机模式

![手机模式](http://upload-images.jianshu.io/upload_images/2024647-6d3a2711600fff00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **Elements**

左边是 HTML 源码，右边是 CSS 样式。可以手动修改、禁用一些 CSS 样式进行查看

![](http://upload-images.jianshu.io/upload_images/2024647-92673f76b3b63a35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **Console**

**控制台输出的 console.log 等都会在这里显示。**百度这里还有个招聘信息，感兴趣的可以试试 ：）

![](http://upload-images.jianshu.io/upload_images/2024647-8817e85a60ccbf76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **Sources**

这个我主要用来**打断点**。Command + P 搜索到你要找的文件，并打断点。Windows 里应该是 Ctrl + P 吧。

比如一个 ionic 项目，我们要给 app.component.ts 这个文件打断点调试，直接找到该文件，打断点运行。这个在 debug 的时候非常好用。

![](http://upload-images.jianshu.io/upload_images/2024647-1b64749a97ed545c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **Network**

**查看一些网络请求。分别会列举名字、状态码、类型、大小、加载耗时等。**

![](http://upload-images.jianshu.io/upload_images/2024647-4eb3e01c1bd3971d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`HTTP 状态码`这里简单列举一下不同字头的意思：

    消息（1字头）
    成功（2字头）
    重定向（3字头）
    请求错误（4字头）
    服务器错误（5字头）

再放个完整版：
[HTTP 状态码大全](https://baike.baidu.com/item/HTTP状态码/5053660?fr=aladdin)

接下来我们点击 🚫 图标清除一下已显示的数据。并点击百度顶部的新闻按钮，并在 Network 中选择 XHR（XMLHttpRequest），就可以看到他的 XHR 网络请求。右下侧可以选择 Headers、Preview、Response 等。

这里不过多讲解，想深入了解 HTTP 相关知识的，可以去看看[《图解HTTP》](https://book.douban.com/subject/25863515/)。

![](http://upload-images.jianshu.io/upload_images/2024647-dfb714b158455454.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

调试安卓应用 Web 页面
---
拿一个我的 ionic Demo 举例。

    1.打开 USB 调试、允许文件传输
    2.手机装上你要测试的应用
    3.在 Chrome 复制进这段话
    chrome://inspect/#devices
    4.运行该应用

**首次使用 inspect 的时候，有可能会出现画面空白的问题。原因是 Chrome 需要下载一些工具，翻墙使用一次后即可解决**

你会在网页上看到你正在运行的应用，我们点击 `inspect`
![](http://upload-images.jianshu.io/upload_images/2024647-bfd25a73d2910e96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 接下来就会在网页上打开你手机的应用，你可以通过点击网页来控制手机，右边的功能和前面讲的操作技巧一样，`可以打断点、看网络请求等`。缺点是可能有点卡

![inspect](http://upload-images.jianshu.io/upload_images/2024647-be1e261ccdee1479.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

彩蛋
---
在最后分享给大家几个实用的工具。

- **[修改 hosts 上 Google 查资料](https://laod.cn)**
比如 ionic 中文资料太少，找起来还是 Google 方便。

- **Chrome拓展程序：AdBlockPlus**
屏蔽广告的神器，更多工具 -> 拓展程序 里，搜索 ad 应该就能找到他了，如果被墙了用上面那个改 hosts 的就能解决。

![使用前](http://upload-images.jianshu.io/upload_images/2024647-d616205cc4664252.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![使用后](http://upload-images.jianshu.io/upload_images/2024647-181f8a7e848e5e5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

临时找的图，这还不算明显的，有的网站使用前后那叫一个夸张。这个扩展还有一个好处，他可以自定义过滤元素，遇到漏网之鱼手动屏蔽之后会记录起来的。

- **Chrome拓展程序：Infinity新标签页**
以前不爱用 Chrome 就是他打开新标签页不太习惯，这个插件完美解决了这个问题。包括图标自定义，界面自定义都很好用。不多介绍，试一试吧，适合自己的才是最好的。
