---
layout: pages
title: iOS 属性关键字介绍
date: 2016.11.22 17:05
tags: iOS
---

![](http://upload-images.jianshu.io/upload_images/2024647-54621f7e2eb9bd01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####assign：
    一般用于基本数据类型、枚举、结构体等非OC对象类型。

####copy：
    一般用于 NSString 类型、block 上。
**`为什么 NSString 使用 copy 而不使用 strong？`**  
区别不大，个人觉得可以防止 NSMutableString 被无意中修改。  
修改方法见下面介绍的的关于 strong 和 copy 的一些区别

####strong：
    strong 与 weak 是因为 ARC 出现而新引入的对象变量属性。
    strong 和 retain 同义, weak 和 assign 同义。
    一般用于 OC 对象类型（NSArray、NSDate、NSNumber、模型类）和 UI 控件

<!-- more -->

**`关于 strong 和 copy 的一些区别`**  
下面举一个例子

	@property (nonatomic, copy) NSString *strCopy1;
	@property (nonatomic, copy) NSString *strCopy2;
	@property (nonatomic, copy) NSString *strCopy3;
	@property (nonatomic, strong) NSString *strStrong;
	
	
	NSMutableString *str = [NSMutableString stringWithFormat:@"1"];
	_strStrong = str;
	_strCopy1 = str;
	self.strCopy2 = str;
	_strCopy3 = [str copy];
	NSLog(@"_strStrong：%@ 地址：%p", _strStrong, &_strStrong);
	NSLog(@"_strCopy1：%@ 地址：%p", _strCopy1, &_strCopy1);
	NSLog(@"_strCopy2：%@ 地址：%p", _strCopy2, &_strCopy2);
	NSLog(@"_strCopy3：%@ 地址：%p", _strCopy3, &_strCopy3);
    
	[str appendString:@"2"];
	NSLog(@"_strStrong：%@ 地址：%p", _strStrong, &_strStrong);
	NSLog(@"_strCopy1：%@ 地址：%p", _strCopy1, &_strCopy1);
	NSLog(@"_strCopy2：%@ 地址：%p", _strCopy2, &_strCopy2);
	NSLog(@"_strCopy3：%@ 地址：%p", _strCopy3, &_strCopy3);
	
输出结果：

	_strStrong：1 地址：0x7ff31cc0c3d0
	_strCopy1：1 地址：0x7ff31cc0c3b8
	_strCopy2：1 地址：0x7ff31cc0c3c0
	_strCopy3：1 地址：0x7ff31cc0c3c8
	
	_strStrong：12 地址：0x7ff31cc0c3d0
	_strCopy1：12 地址：0x7ff31cc0c3b8
	_strCopy2：1 地址：0x7ff31cc0c3c0
	_strCopy3：1 地址：0x7ff31cc0c3c8
	
结论：  
1.用等号直接赋值，会把内存地址一起复制过去。所以 str 改变时 _strStrong 也会跟着改变。  
2.strCopy1 虽然声明为 copy 属性，但是没有用 self 的点语法，所以**没有调用到自身的 set 方法**。可参考 strCopy2 的输出。  
3.strCopy3 是手动使用 copy 方法，所以也可以得到 1 这个结果。

####weak：
    一般用于 delegate，weak 比 assign 多了一个功能，当对象消失后自动把指针变成 nil。
在防止如 block 的循环引用时，使用 `__weak` 关键字做如下定义：  

	__weak typeof(self) weakSelf = self;
也可以定义一个宏：

	#define WeakSelf __weak typeof(self) weakSelf = self;


####atomic：
    1.默认属性
    2.安全。会保证 CPU 不会出现某一线程执行完 setter 全部语句之前，另一个线程开始执行 setter 情况
    3.速度不快，因为要保证操作整体完成

####nonatomic：
    1.非默认属性，但是最常用
    2.线程不安全，如有两个线程访问同一个属性，会出现无法预料的结果
    3.速度更快

atomic 和 nonatomic 自动生成的 set 方法详解：

    // @property(nonatomic, retain) UITextField *userName;
    // 系统生成的代码如下：

    - (UITextField *)userName {
      return userName;
    }

    - (void)setUserName:(UITextField *)userName_ {
      [userName_ retain];
      [userName release];
      userName = userName_;
    }

而 atomic 版本的要复杂一些：

    // @property(retain) UITextField *userName;
    // 系统生成的代码如下：

    - (UITextField *)userName {
      UITextField *retval = nil;
      @synchronized(self) {
        retval = [[userName retain] autorelease];
      }
      return retval;
    }

    - (void)setUserName:(UITextField *)userName_ {
      @synchronized(self) {
        [userName release];
        userName = [userName_ retain];
      }
    }
>简单来说，就是 atomic 会加一个锁来保障线程安全，并且引用计数会 +1，来向调用者保证这个对象会一直存在。假如不这样做，如有另一个线程调 setter，可能会出现线程竞态，导致引用计数降到 0，原来那个对象就释放掉了。
