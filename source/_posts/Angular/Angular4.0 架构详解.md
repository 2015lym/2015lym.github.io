---
layout: pages
title: Angular4.0 架构详解
date: 2018-03-19 21:13:02
tags: Angular
---

很久没写这种比较偏概念类的东西了，不过我觉得没有形成一个知识架构，学习效率会大打折扣。在这里把我的所理解的知识分享给大家，部分内容引用自官方文档。下面进入主题

Angular架构概览
---
先来看一下官方放出的架构图。

![架构概览.png](http://upload-images.jianshu.io/upload_images/2024647-f55b22de51b5ba1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个架构图展现了 Angular 应用中的 8 个主要构造块：

- 模块 (module)
- 组件 (component)
- 模板 (template)
- 元数据 (metadata)
- 数据绑定 (data binding)
- 指令 (directive)
- 服务 (service)
- 依赖注入 (dependency injection)

接下来我按顺序并结合图来讲解一下。

<!-- more -->

1.模块 (module)
---
Angular 或者 ionic 新建的项目，都会存在一个根模块，默认名是  AppModule。如果你使用了模块化来创建应用，包括 AppModule，每个都会存在一个 @NgModule 装饰器的类(虽然他很像 java 中的注解，但是他的官方命名叫装饰器)。我们新建的页面，如果不使用懒加载，都要`在 @NgModule 中先声明后使用`。

下面举个例子，简单介绍一下 @NgModule 中的内容

	//app.module.ts

	import { NgModule }      from '@angular/core';
	import { BrowserModule } from '@angular/platform-browser';
	@NgModule({
	  imports:      [ BrowserModule ],
	  providers:    [ Logger ],
	  declarations: [ AppComponent ],
	  exports:      [ AppComponent ],
	  bootstrap:    [ AppComponent ]
	})
	export class AppModule { }

- imports 本模块声明的组件模板需要的类所在的其它模块。

- providers 服务的创建者，并加入到全局服务列表中，可用于应用任何部分。

- declarations 声明本模块中拥有的视图类。Angular 有三种视图类：**组件、指令和管道**。

- exports declarations 的子集，可用于其它模块的组件模板。

- bootstrap 指定应用的主视图（称为根组件），它是所有其它视图的宿主。只有根模块才能设置 bootstrap 属性。

>在图中的意义：看图左上角，模块是用来接收一个用来描述模块属性元数据对象的。

2.组件 (component)、模板 (template)、元数据 (metadata)
---
这次我们把这三点一起来讲，先看一下这段代码

	//hero-list.component.ts

	@Component({
	selector:    'hero-list',
	templateUrl: './hero-list.component.html',
	providers:  [ HeroService ]
	})
	export class HeroListComponent implements OnInit {
	/* . . . */
	}

**Component**  
**组件**是一个装饰器，他能接受一个配置对象， Angular 会基于这些信息创建和展示组件及其视图。


- selector：CSS 选择器，它告诉 Angular 在父级 HTML 中查找 <hero-list> 标签，创建并插入该组件。 
- templateUrl：组件 HTML 模板的模块相对地址，如果使用 template 来写的话是用“`”这个符号来直接编写 HTML 代码。
- providers：组件所需服务的依赖注入。

**template**  
**模板**就是那段 HTML 代码，可以用 templateUrl 引入外面的，也可以用 template`` 直接写。

**metadata**  
**元数据**装饰器用类似的方式来指导 Angular 的行为。 例如 @Input、@Output、@Injectable 等是一些最常用的装饰器，用法就不在这里展开了。


>在图中的意义：看图中间那一块，模板、元数据和组件共同描绘出这个视图。

3.数据绑定 (data binding)
---
这里一共展示四种数据绑定，看一下示例代码：

	//插值表达式    显示组件的hero.name属性的值
	<li>{{hero.name}}</li>
	
	//属性绑定    把父组件selectedHero的值传到子组件的hero属性中
	<hero-detail [hero]="selectedHero"></hero-detail>
	
	//事件绑定    用户点击英雄的名字时调用组件的selectHero方法
	<li (click)="selectHero(hero)"></li>
	
	//双向绑定    数据属性值通过属性绑定从组件流到输入框。用户的修改通过事件绑定流回组件，把属性值设置为最新的值
	<input [(ngModel)]="hero.name">
	
可能大家对各种括号看的眼花了，**总结一下：**

1. 双花括号是单向绑定，传递的是值。方向是 `组件 -> 模板`。
2. 方括号是单向绑定，传递的是属性。方向是 `父组件 -> 子组件`。
3. 圆括号是事件绑定，处理`点击等活动（action）`。
4. 方括号套圆括号是双向绑定，方向是 `组件 <-> 模板`。

>在图中的意义：看图中间那一块，数据绑定给模板和组件提供数据交互的方式。

4.指令 (directive)
---
`严格来说组件就是一个指令`，但是组件非常独特，并在 Angular 中位于中心地位，所以在架构概览中，我们把组件从指令中独立了出来。
我们这里提到的指令有两种类型：  
结构型 structural 指令和属性 attribute 型指令。  
放一下原文证明一下组件确实算是一个指令：  

While a component is technically a directive, components are so distinctive and central to Angular applications that this architectural overview separates components from directives.  
Two other kinds of directives exist: structural and attribute directives.

**结构型指令**是 ngFor、ngIf 这种的，`通过在 DOM 中添加、移除和替换元素来修改布局。`

**属性型指令**是 ngModel 这种的，`用来修改一个现有元素的外观或行为。`

Angular 还有少量指令，它们或者修改结构布局（例如  ngSwitch ）， 或者修改 DOM 元素和组件的各个方面（例如 ngStyle 和 ngClass）。之后我会单独写一篇讲他们用法的文章。

>在图中的意义：看图右上角那一块，组件是一个带模板的指令，只是扩展了一些面向模板的特性。

5.服务 (service)
---
官方文档的概念：  
服务是一个广义范畴，包括：值、函数，或应用所需的特性。服务没有什么特别属于 Angular 的特性。Angular 对于服务也没有什么定义，它甚至都没有定义服务的基类，也没有地方注册一个服务。

这就像你在 iOS 或者 Android 里单独建了一个类叫 httpService ，封装了网络请求服务一样，不是具体的什么东西，就是一个概念了解下就行。

>在图中的意义：看图左下角角那一块，服务是**用来封装可重用的业务逻辑**。

6.依赖注入 (dependency injection)
---
依赖注入是提供类的新实例的一种方式，还负责处理类所需的全部依赖。**大多数依赖都是服务**。 Angular 使用依赖注入来提供新组件以及组件所需的服务。


比如我们要给某组件导入 HeroService 这个服务，看这段代码：

	constructor(private service: HeroService) { 
		...
	}

这个constructor就是`构造函数`，依赖注入在 constructor 中进行。你也许会觉得前面写上 private、public 之类的很怪，这是 TypeScript 语法比较特殊，习惯就好。

当 Angular 创建组件时，会首先为组件所需的服务请求一个注入器 injector。我们必须先用注入器 injector 为 HeroService 注册一个提供商 provider。

意思就是我们必须在 providers 写上才能用，看这段代码：

	@Component({
	  selector:    'hero-list',
	  templateUrl: './hero-list.component.html',
	  providers:  [ HeroService ]
	})
	
>在图中的意义：看图左下角角那一块，依赖注入主要用来导入服务。

**最后给大家推荐一些学习资源**

[《Angular权威教程》](http://www.ituring.com.cn/book/1874)  
书内容不错，由浅入深，从网上买的正版纸质书，习惯PDF的朋友可以搜索一下电子书资源。

[Angular2.0视频教程](https://my.oschina.net/mumu/blog/834254)  
大漠穷秋出的教程，必看。

[Angular中文文档](https://www.angular.cn/docs/ts/latest/)  
官方文档，内容很全，需要一定的时间进行阅读。

[TypeScript中文文档](https://www.tslang.cn/docs/handbook/basic-types.html)  
官方文档，至少要把 TS 的基本语法要去掌握了。

[Rxjs官方文档中文翻译](https://www.gitbook.com/book/buctwbzs/rxjs/details)  
翻译质量不错，值得一看。

Angular 现在出到 4.0 了，大家有可能会担心网上 Angular2.0 相关的内容会过时，其实不必有这种担心。  
按照官方的说法，从 2.0 版本开始出的新版本会保持向后兼容，不会出现 1 到 2 这么大的变化了，大家可以放心学习。

>好了，Angular 架构概览的八个主要点就是这些。有什么错误或问题可以在下方留言
