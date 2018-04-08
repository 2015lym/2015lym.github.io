---
layout: pages
title: Angular4.0 内置指令全攻略
date: 2018-03-19 21:13:02
tags: Angular
---

![](http://upload-images.jianshu.io/upload_images/2024647-21a1ad6c1a23c6fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这篇文章中，我们将分别列举每一个内置指令的用法，并提供一个例子作为演示。尽量用`最少最简单`的描述，让你在`更快更准确`地学会每一种内置指令的基本用法。

<!-- more -->

ngFor
---
>作用：像 for 循环一样，可以重复的从数组中取值并显示出来。

**例子：**

    // .ts

    this.userInfo = ['张三', '李四', '王五'];
	
    // .html

    <div class="ui list" *ngFor="let username of userInfo">
        <div class="item">{{username}}</div>
    </div>

**讲解：**  
他的语法是 `*ngFor="let username of userInfo"`，其中 userInfo 是从中取值的数组，username 是每次从中取出来的值。然后在这个标签里面的内容就会重复执行，并通过双向绑定，将 username 显示出来。

ngIf
---
>作用：根据条件决定是否显示或隐藏这个元素。

**例子：**

    // .html
	
    <div *ngIf="false"></div>
    <div *ngIf="a > b"></div>
    <div *ngIf="username == '张三'"></div>
    <div *ngIf="myFunction()"></div>

**讲解：**  

1. 永远不会显示
2. 当 a 大于 b 的时候显示
3. 当 username 等于 张三 的时候显示
4. 根据 myFunction() 这个函数的返回值，决定是否显示

ngSwitch
---
>作用：防止条件复杂的情况导致过多的使用 ngIf。

**例子：**

    // .html
	
    <div class="container" [ngSwitch]="myAge">
        <div *ngSwitchCase="'10'">age = 10</div>
        <div *ngSwitchCase="'20'">age = 20</div>
        <div *ngSwitchDefault="'18'">age = 18</div>
    </div>
	
**讲解：**  
[ngSwitch] 先与目标进行绑定，ngSwitchCase 列出每个可能性，ngSwitchDefault 列出默认值。

ngStyle
---
>作用：可以使用动态值给特定的 DOM 元素设定 CSS 属性。  

**例子：**
	
    // .ts
    backColor: string = 'red';
	
    // .html
    <div [style.color]="yellow">
        你好，世界
    </div>
    <div [style.background-color]="backColor">
        你好，世界
    </div>
    <div [style.font-size.px]="20">
        你好，世界
    </div>
    <div [ngStyle]="{color: 'white', 'background-color': 'blue', 'font-size.px': '20'}">
        你好，世界
    </div>
	
**讲解：**

1. 直接设置颜色为 yellow。
2. 设置背景颜色为 backColor，并可以在 .ts 文件中对 backColor 的值进行修改。
3. 设置字体大小，需要注意的是 `只写 font-size 会报错，必须在后面加上 .px。`当然 .em .% 都是可以的。
4. 前三种都是只设置一个，写 [ngStyle] 可以同时写多个，使用花括号包住里面的内功。需要注意的是`连字符 -` 是不允许出现在对象的键名当中的，如果使用 background-color 等时需要加上单引号。

ngClass
---
>作用：动态地设置和改变一个给定 DOM 元素的 CSS类。

**例子：**

    // .scss
    .bordered {
        border: 1px dashed black;
        background-color: #eee;
    }

    // .ts
    isBordered: boolean = true;
		
    // .html
    <div [ngClass]="{bordered: isBordered}">
        是否显示边框
    </div>
	
**讲解：**

- scss 中设置了样式，相当于你建了一个 class="bordered"。  
- ts 中新建了一个 isBordered，用于判断是否显示 .scss 中的样式。
- html 中用 isBordered 作为 `bordered 是否显示` 的判断依据。

ngNonBindable
---
>作用：告诉 Angular 不要绑定页面的某个部分。

**例子：**

    .html
	
    <div ngNonBindable>
        {{我不会被绑定}}
    </div>
	
**讲解：**  
使用了 ngNonBindable ，花括号就会被当做字符串一起显示出来。

总结
---
日常开发中，用 ngFor 和 ngIf 应该是最多的了，所以把他们两个写在了前面。至于 ngNonBindable，我实际开发中一次没用过，也是查着资料测试一遍写下来的。😓

喜欢的可以点个赞，有问题的可以在下方留言。
