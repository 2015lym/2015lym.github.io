---
layout: pages
title: ionic3 教程（四）安卓硬件返回键处理
date: 2017.09.02 12:59
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-63a44e283f27be6a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这次要讲的功能主要依赖于两个插件：
**File Transfer**  和 **File Opener**  

插件介绍
---
> 主要功能

- **File Transfer**  
功能：文件上传、文件下载

- **File Opener**  
功能：文件预览

> 支持的平台

平台名  | File Transfer | File Opener
----    |:------:    | :----: 
Amazon Fire OS | √ | ×
Android | √ | √
Browser | √ | ×
iOS | √ | √
Ubuntu | √ | ×
Windows | √ | √
Windows Phone 7 | √ | ×
Windows Phone 8 | √ | √

`注：安卓最低支持到 4.4.4 版本`

<!-- more -->

虽然支持的都挺多，不过既然是 ionic 项目，一会我们主要讨论 iOS 和 Android

> 安装方式

只作为 cordova 插件：

    // File Transfer
    $ cordova plugin add cordova-plugin-file-transfer
	
    // File Opener
    $ cordova plugin add cordova-plugin-file-opener2	
ionic 中使用：

    // File Transfer
    $ ionic cordova plugin add cordova-plugin-file-transfer
    $ npm install --save @ionic-native/file-transfer
	
    // File Opener
    $ ionic cordova plugin add cordova-plugin-file-opener2
    $ npm install --save @ionic-native/file-opener

为什么在 ionic 中安装有区别呢？  
第一行：ionic 把 cli 从 cordova 改成了 ionic cordova  
第二行：使用 npm 这个命令把包名保存在 package.json 中

> 官方地址

- [File Transfer](https://github.com/apache/cordova-plugin-file-transfer)
- [File Opener](https://github.com/pwlin/cordova-plugin-file-opener2)

想去里面看 issue 查问题的需要注意一下，File Transfer 的 issue 已经关了，让去另一个网站提问。README.md 原话  
Report issues with this plugin on the [Apache Cordova issue tracker](https://issues.apache.org/jira/issues/?jql=project%20%3D%20CB%20AND%20status%20in%20%28Open%2C%20%22In%20Progress%22%2C%20Reopened%29%20AND%20resolution%20%3D%20Unresolved%20AND%20component%20%3D%20%22Plugin%20File%20Transfer%22%20ORDER%20BY%20priority%20DESC%2C%20summary%20ASC%2C%20updatedDate%20DESC)

使用方法
---
> 准备工作

上面两个安装好后。还需要使用安装另一个 File 插件，因为只用到了一句话，所以上面没把他列出来

    $ ionic cordova plugin add cordova-plugin-file
    $ npm install --save @ionic-native/file

都安装好后，**把他们添加到 app.module.ts 的 providers**

> File Transfer 通用部分

    import { FileTransfer, FileTransferObject, FileUploadOptions } from '@ionic-native/file-transfer';
    import { File } from '@ionic-native/file';

    constructor(
      ...
      private transfer: FileTransfer,
      private file: File,
      ...) {...}

> File Transfer 上传文件

    upload() {
      // ionic 官方文档例子漏写了这句话
      // http://ionicframework.com/docs/native/file-transfer/
      //
      const fileTransfer: FileTransferObject = this.transfer.create();
      // 更多的 Options 可以点进去自己看看，不懂的就谷歌翻译他的注释
      let options: FileUploadOptions = {
        fileKey: 'file',
        fileName: 'name.jpg',  // 文件类型
        headers: {},
        params: {}    // 如果要传参数，写这里
        .....
      }

      fileTransfer.upload('<file path>', '<api endpoint>', options)
      .then((data) => {
        // success
      }, (err) => {
        // error
      })
    }

> File Transfer 下载文件

先放出 ionic 官方文档例子。

    download() {
      const url = 'http://www.example.com/file.pdf';
      fileTransfer.download(url, this.file.dataDirectory + 'file.pdf').then((entry) => {
        console.log('download complete: ' + entry.toURL());
      }, (error) => {
        // handle error
      });

这个 pdf 不知道为啥我一直下载失败，我去别的网站随便找个 pdf 或者项目上的文件下载一直成功，大家可以自己试试。

> File Transfer 下载文件保存路径问题

这个保存路径，经过我测试 iOS 保存成功，安卓有的手机成功有的失败。检查了一下 AndroidManifest.xml 里的权限，权限代码也自动添加了

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

安卓 7.0 保存成功，4.4.4 和 5.3.3 都失败了。因为 `Android 从 6.0 运行时的权限修改了`，我不知道和这个有没有关系。暂时用了一个方法解决了问题，因为测试机有限，测的可能也不太完整，有问题请指出。

我判断了一下，如果是 iOS 或者 Android 6 及以上的版本，则保存路径使用

    this.file.dataDirectory + 'file.pdf'

否则使用

    'file:///storage/sdcard0/Download/' + 'file.pdf'

> File Transfer 显示进度

直接复制这段代码。我做的项目，在下载的时候用了，感觉上传应该也能用。

    // 进度
    fileTransfer.onProgress(progressEvent => {
      if (progressEvent.lengthComputable) {
        // 下载过程会一直打印，完成的时候会显示 1
        console.log(progressEvent.loaded / progressEvent.total);
      } else {

      }
    });

> File Opener 预览文件（可配合文件下载使用）

    import { FileOpener } from '@ionic-native/file-opener';

    constructor(
      ...
      private fileOpener: FileOpener,
      ...) {...}

在文件下载成功的地方插入以下代码

    // entry.nativeURL 是上面那个插件文件下载后的保存路径
    this.fileOpener.open(entry.nativeURL, this.getFileMimeType(fileType))
      .then(() => {
        console.log('打开成功');
      })
      .catch(() => {
        console.log('打开失败');
      });

**重点：  
这里的第二个参数不是文件类型，而是 [MIMEType](https://baike.baidu.com/item/MIME/2900607?fr=aladdin)**

这个插件在 iOS 上好像不需要提供 MIME，我传错了也能预览，安卓是全军覆没了。这里提供一个代码里用到的取得 MIME 的方法

    getFileMimeType(fileType: string): string {
      let mimeType: string = '';

      switch (fileType) {
        case 'txt':
          mimeType = 'text/plain';
          break;
        case 'docx':
          mimeType = 'application/vnd.openxmlformats-officedocument.wordprocessingml.document';
          break;
        case 'doc':
          mimeType = 'application/msword';
          break;
        case 'pptx':
          mimeType = 'application/vnd.openxmlformats-officedocument.presentationml.presentation';
          break;
        case 'ppt':
          mimeType = 'application/vnd.ms-powerpoint';
          break;
        case 'xlsx':
          mimeType = 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet';
          break;
        case 'xls':
          mimeType = 'application/vnd.ms-excel';
          break;
        case 'zip':
          mimeType = 'application/x-zip-compressed';
          break;
        case 'rar':
          mimeType = 'application/octet-stream';
          break;
        case 'pdf':
          mimeType = 'application/pdf';
          break;
        case 'jpg':
          mimeType = 'image/jpeg';
          break;
        case 'png':
          mimeType = 'image/png';
          break;
        default:
          mimeType = 'application/' + fileType;
          break;
      }
      return mimeType;
    }

获取文件类型的方法也给一个

    // 刚开始造轮子的时候写的是 indexOf
    // 后来果然出问题了，还是得判断最后一个 .
    getFileType(fileName: string): string {
      return fileName.substring(fileName.lastIndexOf('.') + 1, fileName.length).toLowerCase();
    }

文件预览的效果，在 iOS 上是直接调用了预览功能。安卓上是弹框选择打开软件，基本上 WPS Office 都能搞定。
