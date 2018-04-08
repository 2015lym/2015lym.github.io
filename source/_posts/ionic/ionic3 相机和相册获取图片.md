---
layout: pages
title: ionic3 教程（四）安卓硬件返回键处理
date: 2017.09.10 14:14
tags: ionic
---

![](http://upload-images.jianshu.io/upload_images/2024647-4cc6982b140505b9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**相机拍照**和**相册**是 App 从手机获取图片的两种主要方式。在 App 业务需求中，经常会使用到图片获取的功能，最为常见的有用户头像设置等。  
既然是常用功能，这一次我们就来分析一下如何在 ionic3 中使用相机和相册获取图片。

#目录：
- 插件介绍
- 使用方法
- 图片上传
- 注意事项

插件介绍
---
> [cordova-plugin-camera](https://github.com/apache/cordova-plugin-camera) 相机插件：

<!-- more -->

安装方法：

    $ ionic cordova plugin add cordova-plugin-camera
    $ npm install --save @ionic-native/camera

作用：    
调用手机照相头，进行拍照获取图片、从相册获取单张图片。

> [cordova-plugin-image-picker](https://github.com/wymsee/cordova-imagePicker) 图片获取插件：

安装方法：

    // 注意： 
    // 这个 "your usage message" 可以自己修改
    // 这是在向用户请求权限时显示的文字

    $ ionic cordova plugin add cordova-plugin-telerik-imagepicker --variable PHOTO_LIBRARY_USAGE_DESCRIPTION="your usage message"
    $ npm install --save @ionic-native/image-picker

作用：  
调用手机图库，获取照片，可选择多张。

使用方法：
---
**先把插件添加到 app.module.ts 的 providers**
> Camera

    import { Camera, CameraOptions } from '@ionic-native/camera';

    constructor( ...
      private camera: Camera) { }

    // 设置选项
    const options: CameraOptions = {
      quality: 100,
      sourceType: this.camera.PictureSourceType.CAMERA,
      destinationType: this.camera.DestinationType.DATA_URL,
      encodingType: this.camera.EncodingType.JPEG,
      mediaType: this.camera.MediaType.PICTURE
    }

    // 获取图片
    this.camera.getPicture(options).then((imageData) => {
    // 获取成功
    let base64Image = 'data:image/jpeg;base64,' + imageData;

    }, (err) => {
      console.log('获取图片失败');
    });

理解起来应该不难，详细解释一下 **CameraOptions**

CameraOptions  | 作用 | 参数 |
----  |------    |----     
quality | 图像质量，越高质量越好 (0-100) | number  
sourceType | 图片源 | PHOTOLIBRARY、CAMERA、SAVEDPHOTOALBUM  
destinationType | 图片路径  | DATA_URL、FILE_URL、NATIVE_URI  
encodingType  | 图片类型  | JPEG、PNG  
mediaType | 媒体类型，这个插件也能选视频等 | PICTURE、VIDEO、ALLMEDIA |

重点说一下 `sourceType`，这个参数设置为 PHOTOLIBRARY 就会从相册取图，设置为 CAMERA 会拍照，设置为 SAVEDPHOTOALBUM 会保存图片。

> ImagePicker


    import { ImagePicker, ImagePickerOptions } from '@ionic-native/image-picker';


    constructor( ...
      private imagePicker: ImagePicker) { }

    // 设置选项
    const options: ImagePickerOptions = {
      maximumImagesCount: 6,
      width: IMAGE_SIZE,
      height: IMAGE_SIZE,
      quality: QUALITY_SIZE
    };

    // 获取图片
    this.imagePicker.getPictures(options).then((results) => {
      for (var i = 0; i < results.length; i++) {
          console.log('Image URI: ' + results[i]);
      }
    }, (err) => {
      console.log('获取图片失败');
    });

跟上面那个差不多，详细解释一下 ** ImagePickerOptions**

ImagePickerOptions  | 作用 | 参数 
---- |------    |----  
maximumImagesCount | 最多选几张，默认 15 | number 
width | 图片宽度 | number 
height | 图片高度  | number 
quality | 图像质量，越高质量越好 (0-100) | number 
outputType | 输入类型，默认为 0 (FILE_URI) | number 

图片上传
---
如果是 base64 图片上传，直接把二进制字符串作为参数传就好。
另一种方式就按照 [ionic3 文件上传下载和预览](http://www.jianshu.com/p/e5631e241a73) 的方法来就行

注意事项
---
> iOS 无法打开相册

再执行一遍

    $ ionic cordova plugin add cordova-plugin-telerik-imagepicker --variable PHOTO_LIBRARY_USAGE_DESCRIPTION="your usage message"

我们执行过后，会有一个插件 `com.synconset.imagepicker` 生成到 package.json 中，这个会导致 npm install 运行失败，解决方法只能把它删除。所以你再次安装的时候就会少一个这个插件，导致无法获取相册权限。Android 上没有这个问题。

参考资料：  
https://github.com/Telerik-Verified-Plugins/ImagePicker/issues/71#issuecomment-323993112

> ImagePickerOptions 的类型 maximumImagesCount：

源代码中的注释：

    max images to be selected, defaults to 15. If this is set to 1, 
    upon selection of a single image, the plugin will return it. (Android only)

默认 15张没问题，设置为 1 的时候，在安卓上插件会返回。我并没有没试过，遇到问题的朋友可以参考这个注释

> ImagePickerOptions 的类型 outputType：

源代码中的注释：

    Output type, defaults to 0  (FILE_URI).

并没有说清除传其他数字会怎么样，我们使用默认值就好。
