---
layout: pages
title: UILabel 实现长按复制
date: 2017.10.24 10:07
tags: iOS
---

![](http://upload-images.jianshu.io/upload_images/2024647-3bffe50fe9f0676d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 UILabel 上实现长按复制，我用的是 UIMenuController。在 UITextView、UITextField 中，已经自带了这个东西，但是在 UILabel 上需要自定义。鉴于有的朋友很少接触 UIMenuController，这里先介绍一些基本知识。

UIMenuController 可以使用系统自带的方法，也可以自定义。

常见的系统方法和使用
---
	- (void)cut:(nullable id)sender NS_AVAILABLE_IOS(3_0);
	- (void)copy:(nullable id)sender NS_AVAILABLE_IOS(3_0);
	- (void)paste:(nullable id)sender NS_AVAILABLE_IOS(3_0);
	- (void)select:(nullable id)sender NS_AVAILABLE_IOS(3_0);
	- (void)selectAll:(nullable id)sender NS_AVAILABLE_IOS(3_0);
	- (void)delete:(nullable id)sender NS_AVAILABLE_IOS(3_2);

从字面意思就能看出，他们是剪切、复制、粘贴、选择、全选、删除。使用方法很简单。

<!-- more -->

	// 比如我在一个 UITextView 里，想增加全选和复制的方法
	// 只要在自定义 UITextView 的时候加入这行代码即可
	- (BOOL)canPerformAction:(SEL)action withSender:(id)sender {
	    if(action == @selector(selectAll:) || action == @selector(copy:)) return YES;
	    return NO;
	}
	
细心的朋友可能会发现，最后**长按出来的文字都是英文，我们改如何把他改成中文呢**？如图，在 Project -> Info -> Localizations 中添加 Chinese(Simplified) 即可。

![](http://upload-images.jianshu.io/upload_images/2024647-72d2dd668dad3c53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自定义方法和使用
---
回到主题，我们要在 UILabel 上加入长按复制事件，但是他本身是不支持 UIMenuController 的，所以接下来讲讲自定义方法。

> 自定义一个 UILabel，设置label可以成为第一响应者

	- (BOOL)canBecomeFirstResponder {
	    return YES;
	}
	

> 设置长按事件，在初始化的时候调用这个方法

	- (void)setUp {
	    /* 你可以在这里添加一些代码，比如字体、居中、夜间模式等 */
	    self.userInteractionEnabled = YES;
	    [self addGestureRecognizer:[[UILongPressGestureRecognizer alloc]initWithTarget:self action:@selector(longPress)]];
	}

> 长按事件，在里面新建 UIMenuController

	- (void)longPress {
	    
	    // 设置label为第一响应者
	    [self becomeFirstResponder];
	    
	    // 自定义 UIMenuController
	    UIMenuController * menu = [UIMenuController sharedMenuController];
	    UIMenuItem * item1 = [[UIMenuItem alloc]initWithTitle:@"复制" action:@selector(copyText:)];
	    menu.menuItems = @[item1];
	    [menu setTargetRect:self.bounds inView:self];
	    [menu setMenuVisible:YES animated:YES];
	}

> 设置label能够执行那些

	- (BOOL)canPerformAction:(SEL)action withSender:(id)sender {
	    if(action == @selector(copyText:)) return YES;
	    return NO;
	}

	// 如果模仿上面的写以下代码，点击后会导致程序崩溃
	if(action == @selector(selectAll:) || action == @selector(copy:)) return YES; 

> 方法的具体实现

	- (void)copyText:(UIMenuController *)menu {
	    // 没有文字时结束方法
	    if (!self.text) return;
	    // 复制文字到剪切板
	    UIPasteboard * paste = [UIPasteboard generalPasteboard];
	    paste.string = self.text;
	    
	}

最终效果：
![效果](http://upload-images.jianshu.io/upload_images/2024647-3e55befd426e0105.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**附上 [DEMO](https://github.com/2015lym/CopyLabel/tree/master)，自定义的 UILabel 可以直接拖走使用**
