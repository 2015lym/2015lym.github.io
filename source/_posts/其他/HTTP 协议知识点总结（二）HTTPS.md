---
layout: pages
title: HTTP 协议知识点总结（二）HTTPS
date: 2018.05.28 22:09
tags: 其他
---

![https.jpg](https://upload-images.jianshu.io/upload_images/2024647-8e91a21915e1b405.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上回说到了 **HTTP 与 TCP/IP、常见的 HTTP 方法、HTTP 状态码与HTTP 报文首部字段**。这回主要讲讲 **HTTP 与 HTTPS**。

## HTTP 存在的缺点
在安全方面，HTTP 存在以下几种缺点。

1. **窃听风险：**由于通讯使用明文传输，内容可能会泄露。
2. **篡改风险：**第三方对传输的数据进行篡改，影响与服务端之间的正确通信。
3. **冒充风险：**可能会出现中间人攻击，第三方冒充服务器。

其实上面三种问题可以总结为通信未加密，而 HTTPS 的出现则很好的解决了以上问题。

<!-- more -->

## 什么是 HTTPS
与 HTTP 协议的明文传输相比，HTTPS 是将这些内容加密，确保信息传输安全。最后一个字母 S 指的是 SSL（Secure Socket Layer，安全套接层）/TLS（Transport Layer Security，安全传输层协议） 协议，它位于 HTTP 协议与 TCP/IP 协议之间。

HTTPS 使用了**非对称加密**。  
私钥只存在于服务器上，服务器下发的内容不可能被伪造，因为别人都没有私钥，所以无法加密。
所有人都有公钥，但私钥只有服务器有，所以服务器才能看到被加密的内容。

## HTTPS 的工作过程
针对上面的三个缺点，说一下 HTTPS 是如何应对的。
1. HTTPS 使用非对称加密传输密码，使用这个密码加密数据，避免第三方获取内容。
2. 发送方将信息的`哈希值`一起发送过去，接收方会把解密后的数据与哈希值进行对比，避免被篡改。
3. HTTPS 由权威机构颁布 CA（Certificate Authority，电子商务认证授权机构） 证书，使用证书校验机制防止第三方的伪装。

> 什么是哈希值？

哈希值是通过哈希算法压缩后得到的数据值，理论上来说不管多复杂的数据都可以通过哈希算法求得哈希值。比如我们下载的 Android SDK 就会提供一个 SHA-256 校验和，这个就属于哈希算法的一种。

![SHA-256](https://upload-images.jianshu.io/upload_images/2024647-139f8381313b0e35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 如何使用 HTTPS
现在有很多网站都已经广泛使用 HTTPS，比如 `www.baidu.com`。
![](https://upload-images.jianshu.io/upload_images/2024647-f41f66ad76169b30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而且现在 iOS 提交至 App Store 的应用都必须使用 HTTPS 进行网络请求。所以了解如何使用 HTTPS 还是很有必要的。

申请方式很简单了，拿阿里云举例，打开[官网](https://www.aliyun.com)选择安全 -> CA 证书服务，填写信息购买即可。

![阿里云](https://upload-images.jianshu.io/upload_images/2024647-dfdb4176cb7729b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 为什么不一直使用 HTTPS
在上一会我们讲过 HTTP 有**三次握手**，在加入 HTTPS 之后就变成了**四次握手**，所以效率会降低一些，不过还是能接受的。

由于 HTTPS 会降低一定的速度，还有一些额外的成本。所以对于一些不太需要加密的信息，还是有很多企业倾向于选择 HTTP。

## 最后
这个第二篇和第一篇的间隔有点长，自己最近还是有点懒，各位多多包涵。

写博客这种东西，感觉对自己还是有不少帮助的，虽然自己水平不高，但是本来一些比较模糊的知识点，经过查资料、写博客慢慢也有一些进步了。所以呢，这里还是推荐大家尝试一下通过写博客进行知识总结和学习，有的时候只是看还是容易忘的😂。
