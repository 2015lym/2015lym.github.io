---
layout: pages
title: HTTP 协议知识点总结（一）
date: 2018.04.07 10:45
tags: 其他
---

![HTTP](https://upload-images.jianshu.io/upload_images/2024647-3dc813c03af475fc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在许多大公司的面试中，经常会重点考察面试者的**计算机基础知识**。所以对于在**计算机网络、数据结构、操作系统**上花费更多的时间和精力，是完全值得的。HTTP 作为应用最为广泛的网络协议，不论前端和后端都需要经常接触。最近决定对 HTTP 进行了一些学习，所以整理一份笔记并分享给大家。

HTTP (HyperText Transfer Protocol) 通常被我们称为**超文本传输协议**，又译为[超文本转移协议](https://baike.baidu.com/item/超文本转移协议/1675080?fr=aladdin)。它的命名如何，不需要过于纠结，我们更应该重视它的内容。

<!-- more -->

## HTTP 与 TCP/IP

##### OSI 模型与 TCP/IP 模型对比

|OSI  | TCP/IP|
|:----:|:------:|
应用层<br>表示层<br>会话层 | 应用层 | 
传输层 | 传输层 | 
网络层 | 网络层  | 
数据链路层<br>物理层 | 链路层 | 

##### TCP/IP 四层模型分析

HTTP 是 TCP/IP 协议内部的一个子集，在了解它之前我们需要先了解一下 TCP/IP 相关的知识。TCP/IP（Transmission Control Protocol/Internet Protocol）协议分为应用层、传输层、网络层、链路层四层，我简单的来总结举例一下。

> 应用层

**应用层向用户提供一系列的应用服务。**  
比如我们熟悉的 DNS（Domain Name Service，域名解析）、FTP（File Transfer Protocol，文件传输协议），当然还有我们要讲的 HTTP 协议。  
有时在 `App Store` 无法下载应用时，就经常会把 DNS 设置为谷歌的 `8.8.8.8` 或者国内的 `114.114.114.114`，这些都是比较常见的服务。

> 传输层

**传输层提供连接两台计算机之间的数据传输。**  
传输层有两种协议：
TCP（Transmission Control Protocol，传输控制协议）
UDP（User Datagram protocol，用户数据报协议）

> 网络层

**网络层规定了通过什么样的方式将数据包传递给对方。**
网络层协议的代表包括：
IP（Internet Protocol，互联网协议）  
ICMP（Internet Control Message Protocol，互联网消息控制协议）  

因为 IP 并不是完全可靠，无法保证数据被送达。所以需要 ICMP 传输出错报告控制信息。ICMP 最著名的应用应该就是 ping 了，比如我们上不去某个网站，就可以在终端输入测试一下。

    ping www.baidu.com
    // 然后就可以看到一些返回值
    PING www.a.shifen.com (180.149.132.151): 56 data bytes
    64 bytes from 180.149.132.151: icmp_seq=0 ttl=55 time=31.431 ms
    Request timeout for icmp_seq 1
    64 bytes from 180.149.132.151: icmp_seq=2 ttl=55 time=30.530 ms
    64 bytes from 180.149.132.151: icmp_seq=3 ttl=55 time=30.233 ms    

> 链路层

**链路层用来链接网络的硬件部分。**  
网络层传过来的数据在这里被加工成了可被物理层传输的结构包——帧。帧中包括 MAC 地址（Media Access Control）和一些其他信息。放一幅在网上找的图给大家展示一下。

![数据帧结构](https://upload-images.jianshu.io/upload_images/2024647-3823ae3ac054af5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 一个访问百度网站的例子

1. 在浏览器输入 http://www.baidu.com。
2. DNS 通过你输入的地址，找到百度的 IP 地址 14.215.177.38。
3. 通过 ARP 协议寻找 MAC 地址，建立 TCP 连接。
4. 三次握手
    - 发送端：发送带有 SYN 的数据包
    - 接收端：应答，发送带有 SYN/ACK 的数据包
    - 发送端：应答，发送带有 ACK 的数据包
5. 通过返回的数据包显示网页

这个例子简单总结一下，深入的话也写不完（摊手）。

## 常见的 HTTP 方法

| 方法  | 说明 |
|:----|:------|
GET | 获取资源 | 
POST | 传输实体主体 | 
PUT | 传输替换目标资源 | 
DELETE | 删除目标资源 | 
HEAD | 获取报文首部 | 
OPTIONS | 获取支持的方法 | 

##### 常见方法分析

> GET 与 POST 的区别

这也算是面试比较常问的问题了，这里放上一个表格。

|   | GET | POST |
|:----|:------|:------|
后退按钮/刷新 | 无害 | 数据会被重新提交（浏览器会提示） | 
书签/缓存/历史 | √ |  X | 
编码类型 | application/x-www-form-urlencoded |  application/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码 | 
数据长度限制 | URL 的最大长度是 2048 个字符 |  无限制 | 
数据类型限制 | 只允许 ASCII 字符 |  无限制 | 
可见性 | 数据在 URL 中对所有人可见、请求会保存在历史记录 |  数据保存在主体中、请求不会保存在历史记录 | 

> PUT 请求

`PUT` 与 `POST` 方法的区别在于，PUT方法是幂等的：调用一次与连续调用多次是等价的（即没有副作用），而连续调用多次POST方法可能会有副作用，比如将一个订单重复提交多次。

> DELETE 请求

如果 `DELETE `方法成功执行，那么可能会有以下几种状态码：

*   状态码  `202` (Accepted) 表示请求的操作可能会成功执行，但是尚未开始执行。
*   状态码 `204` (No Content) 表示操作已执行，但是无进一步的相关信息。
*   状态码  `200` (OK) 表示操作已执行，并且响应中提供了相关状态的描述信息。

> HEAD 请求

HEAD 请求就如同前面表格所说的是获取报文首部，该请求方法的一个使用场景是在下载一个大文件前先获取其大小再决定是否要下载, 以此可以节约带宽资源。

> OPTIONS 请求

OPTIONS 请求就是用于获取目的资源所支持的通信选项，平时在做 ionic、Angular 的时候，它就经常会在请求前自动调用一个 OPTIONS 方法。

## HTTP 状态码

简单的说，HTTP 状态码就是**描述返回的请求结果**。
这里还是拿一个表格展示一下，由于种类比较多，所以只列举种类，不进行完全列举。

|   | 类别 | 原因 |
|:----|:------|:------|
1xx | Informational（信息性状态码） | 接收的请求正在处理 | 
2xx | Success（成功状态码） |  请求正常处理完毕 | 
3xx | Redirection（重定向状态码） | 需要进行附加操作以完成请求 | 
4xx | Client Error（客户端错误状态码） | 服务器无法处理请求 | 
5xx | Server Error（服务端错误状态码） |  服务器处理请求出错 | 

## HTTP 报文首部字段

HTTP 报文的首部字段，主要是用来**传递额外的重要信息**。

##### 一个简单的请求例子

前面讲了这么多，这里结合请求方法、状态码等举一个例子。

发起请求：

    GET / HTTP/1.1
    Request URL: https://www.baidu.com/favicon.ico
    Host: www.baidu.com
    Accept-Language: zh-CN

服务端返回：

    HTTP/1.1 200 OK
    Date: Sat, 07 Apr 2018 02:17:48 GMT
    Server: Apache
    Last-Modified: Mon, 02 Apr 2018 09:39:34 GMT
    Accept-Ranges: bytes
    Content-Length: 984
    Content-Type: image/x-icon

现在我们分析一下这个例子。

> 发起请求

    // 请求方法 / HTTP 版本号
    GET / HTTP/1.1
    // 请求地址
    Request URL: https://www.baidu.com/favicon.ico
    // 请求资源所在服务器
    Host: www.baidu.com
    // 优先选择的语言（自然语言）
    Accept-Language: zh-CN

> 服务端返回

    // HTTP 版本、HTTP 状态码 200
    HTTP/1.1 200 OK
    // 创建报文的日期
    Date: Sat, 07 Apr 2018 02:17:48 GMT
    // HTTP 服务器的安装信息
    Server: Apache
    // 资源的最后修改时间
    Last-Modified: Mon, 02 Apr 2018 09:39:34 GMT
    // 支持字节范围请求
    Accept-Ranges: bytes
    // 实体主体的大小
    Content-Length: 984
    // 实体主体的类型
    Content-Type: image/x-icon

HTTP 首部字段种类非常多，该例子举例了常用的一部分，想了解更多可以研究 [MDN HTTP Headers 文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers) 。

![MDN HTTP Headers](https://upload-images.jianshu.io/upload_images/2024647-284ab7734de44129.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前里面中文翻译不够完整，翻译水平高的朋友可以点击帮忙完善一下，为开源事业出一份力：)

这次就总结到这里了，由于水平有限，难免有些许纰漏。有什么错误请指出，不胜感激。

### 参考文章
[为何HTTP被翻译为“超文本传输协议”是一次历史上的重大翻译错误？](http://www.ituring.com.cn/article/937)
[HTTP 协议入门-阮一峰](http://www.ruanyifeng.com/blog/2016/08/http.html)
[TCP/IP 协议 百度百科](https://baike.baidu.com/item/TCP%2FIP协议/212915?fr=aladdin&fromid=214077&fromtitle=tcp%2Fip#5_2)
[ping 原理与ICMP协议](https://blog.csdn.net/inject2006/article/details/2139149)
 [漫谈网络通信——从OSI网络模型到TCP/IP协议族](http://www.cnblogs.com/ivehd/p/networking.html)
[HTTP 方法-w3school](http://www.w3school.com.cn/tags/html_ref_httpmethods.asp)
[HTTP 请求方法-MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods)
[《图解HTTP》](https://book.douban.com/subject/25863515/)