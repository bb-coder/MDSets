layout: p
title: 内存恶鬼drawRect
date: 2016-01-03 15:26:13
tags: iOS
categories: iOS
---

标题有点吓人，但是对于`drawRect`的评价倒是一点都不过分。在平日的开发中，随意覆盖`drawRect`方法，稍有不慎就会让你的程序内存暴增。下面我们来看一个例子。


去年的某天午后，北京的雾霾依旧像现在这样醇厚，我的同事辉哥像往常一样与我楼下约烟。我见辉哥表情凝重，便询问究竟。辉哥做了一个画板功能，但是苦于内存问题一直得不到解决。画板功能很简单，就是记录手指触摸的轨迹然后绘制在屏幕上。下面我们来看一张效果图：


![效果图](http://7xkdhe.com1.z0.glb.clouddn.com/drawRect.gif)


如图我们看到左侧内存的状况随着手指的绘制逐渐恶化。另外细心的同学可以观察到，点击图中蓝色矩形按钮之后，便会弹出画板，而这时并没有进行任何的手指绘制，内存就突变为`114MB`，然后每当手指绘制开始时，内存立即增加到`300MB`左右稳定下来。对于正常的`iOS App`来讲，这么大的内存消耗是不能容忍的。

<!-- more -->

下面分析一下原因：
可能的原因有两个，一是在手指绘制的过程中创建的大量点对象没有及时释放或者其他资源没有及时释放。
二是系统在绘制的过程中开始大量消耗内存。

第一个原因，手指绘制的过程中创建的大量点对象没有及时释放或者其他资源没有及时释放。这一点我们暂时排除以节省时间，因为这个画板功能工程是用`ARC`写的，并且我们已经做过代码检查和使用`Instruments`工具来检测内存使用情况，这里并没有所谓的对象没有及时释放的问题存在。

第二个原因，系统在绘制的过程中开始大量消耗内存。首先我们曾经注意到一个诡异并且不寻常的事情就是，当黄色的画板刚刚弹出的时候内存就瞬间从`18MB`暴增至`114MB`。这一点更加说明第一个原因不是问题所在，因为这时手指还没有进行任何绘制，也就是说不存在任何点与线的对象，那么内存怎么会暴增呢？

这时我们要考虑这个画板功能是如何实现的，画板分为两步，第一步记录用户手指的轨迹，这一步会生成大量点的对象（已排除嫌疑）。第二步绘制到视图或者图层上，我们平常使用频繁的绘图方式基本上是`Quarz2D`的那套`C`语言框架，而绘制代码所在的地点在哪呢？我们今天的主角终于上场了－－`drawRect`。

下面我们来看一段画板功能绘制的代码：

```objc
	- (void)drawRect:(CGRect)rect
	{
    	if (!self.paths.count) return;
    	CGContextRef ctx = UIGraphicsGetCurrentContext();
    	for (BHBPaintPath *path in self.paths) {
      	  CGContextSaveGState(ctx);
      	  [[UIColor blackColor] set];
      	  [path stroke]; //关键的一步绘制
      	  CGContextRestoreGState(ctx);
   	 }
	} 
```

去掉绘图上下文栈和其余判断边界的代码，我们只是在当前`view`上绘制了`n`条黑色的线。看起来普普通通的绘图方式，怎么会导致内存的剧增呢？我们现在说罪魁祸首是`drawRect`证据并不充分。我们回想画板刚弹出时的内存状况，接下来我们注释掉`drawRect`所有的代码。运行的效果图如下：

![效果图1](http://7xkdhe.com1.z0.glb.clouddn.com/drawRect1.gif)

效果立竿见影，__注释掉`drawRect`之后，内存立刻恢复正常，我们终于抓到了消耗内存的恶鬼，问题就出在对`drawRect`方法的覆盖。__那么抓到了犯人，本文是否应该完结了？非也非也，我们虽说知道了内存暴增的原因，但是我们并没有深入的去分析`drawRect`为什么对内存的影响这么大，而且我们也没有给出问题的解决方案。请接着往下看。

那么现在我们分析一下`drawRect`导致内存暴增的真正原因：

重写`drawRect`为何会导致内存大量上涨？

要想搞明白这个问题，我们需要撸一撸在`iOS`程序上图形显示的原理。在`iOS`系统中所有显示的视图都是从基类`UIView`继承而来的，同时`UIView`负责接收用户交互。__但是实际上你所看到的视图内容，包括图形等，都是由`UIView`的一个实例图层属性来绘制和渲染的，那就是`CALayer`。__

`CALayer`类的概念与`UIView`非常类似，它也具有树形的层级关系，并且可以包含图片文本、背景色等。它与`UIView`最大的不同在于它不能响应用户交互，可以说它根本就不知道响应链的存在，它的API虽然提供了“某点是否在图层范围内的方法”，但是它并不具有响应的能力。

在每一个`UIView`实例当中，都有一个默认的支持图层，`UIView`负责创建并且管理这个图层。实际上__这个`CALayer`图层才是真正用来在屏幕上显示的__，`UIView`仅仅是对它的一层封装，实现了`CALayer`的`delegate`，提供了处理事件交互的具体功能，还有动画底层方法的高级API。

可以说`CALayer`是`UIView`的内部实现细节。

脑补了这么多，它与今天的主题`drawRect`有何关系呢？别着急，我们既然已经确定`CALayer`才是最终显示到屏幕上的，只要顺藤摸瓜，即可分析清楚。`CALayer`其实也只是`iOS`当中一个普通的类，它也并不能直接渲染到屏幕上，因为屏幕上你所看到的东西，其实都是一张张图片。而为什么我们能看到`CALayer`的内容呢，是因为`CALayer`内部有一个`contents`属性。`contents`默认可以传一个`id`类型的对象，但是只有你传`CGImage`的时候，它才能够正常显示在屏幕上。__所以最终我们的图形渲染落点落在`contents`身上__如图。

![效果图2](http://7xkdhe.com1.z0.glb.clouddn.com/drawRect3.001.png)

`contents`也被称为寄宿图，除了给它赋值`CGImage`之外，我们也可以直接对它进行绘制，绘制的方法正是这次问题的关键，通过继承`UIView`并实现`-drawRect:`方法即可自定义绘制。`-drawRect:` 方法没有默认的实现，因为对`UIView`来说，寄宿图并不是必须的，`UIView`不关心绘制的内容。如果`UIView`检测到`-drawRect: `方法被调用了，它就会为视图分配一个寄宿图，这个寄宿图的像素尺寸等于视图大小乘以`contentsScale`(这个属性与屏幕分辨率有关，我们的画板程序在不同模拟器下呈现的内存用量不同也是因为它)的值。

那么回到我们的画板程序，当画板从屏幕上出现的时候，因为重写了`-drawRect:`方法，`-drawRect :`方法就会自动调用。__生成一张寄宿图__后，方法里面的代码利用`Core Graphics`去绘制n条黑色的线，然后内容就会缓存起来，等待下次你调用`-setNeedsDisplay`时再进行更新。

画板视图的`-drawRect:`方法的背后实际上都是底层的`CALayer`进行了重绘和保存中间产生的图片，`CALayer`的`delegate`属性默认实现了`CALayerDelegate`协议，当它需要内容信息的时候会调用协议中的方法来拿。当画板视图重绘时，因为它的支持图层`CALayer`的代理就是画板视图本身，所以支持图层会请求画板视图给它一个寄宿图来显示，它此刻会调用：

```objc
  - (void)displayLayer:(CALayer *)layer;
```
    
如果画板视图实现了这个方法，就可以拿到`layer`来直接设置`contents`寄宿图，如果这个方法没有实现，支持图层`CALayer`会尝试调用：

```objc
  - (void)drawLayer:(CALayer *)layer inContext:(CGContextRef)ctx;
```

这个方法调用之前，`CALayer`创建了一个合适尺寸的空寄宿图（尺寸由`bounds`和`contentsScale`决定）和一个`Core Graphics`的绘制上下文环境，为绘制寄宿图做准备，它作为`ctx`参数传入。在这一步生成的空寄宿图内存是相当巨大的，它就是本次内存问题的关键，一旦你实现了`CALayerDelegate`协议中的`-drawLayer:inContext:`方法或者`UIView`中的`-drawRect:`方法（其实就是前者的包装方法），图层就创建了一个绘制上下文，这个上下文需要的内存可从这个公式得出：`图层宽`\*`图层高`\*`4字节`，宽高的单位均为像素。而我们的画板程序因为要支持像猿题库一样两指挪动的效果，我们开辟的画板大小为：

```objc
_myDrawer = [[BHBMyDrawer alloc] initWithFrame:CGRectMake(0, 0, SCREEN_SIZE.width*5, SCREEN_SIZE.height*2)];
```

我们的画板程序的画板视图它在`iPhone6s plus`机器上的上下文内存量就是 `1920*2`\*`1080*5`\*`4字节`，__相当于`79MB`内存__，图层每次重绘的时候都需要重新抹掉内存然后重新分配。它就是我们画板程序内存暴增的真正原因。

最终我们将内存暴增的原因找出来了，那么我们有没有合理的解决方案呢？

我认为最合理的办法处理类似于画板这样画线条的需求直接用专有图层`CAShapeLayer`。让我们看看它是什么：

`CAShapeLayer`是一个通过矢量图形而不是`bitmap`来绘制的图层子类。用`CGPath`来定义想要绘制的图形，`CAShapeLayer`会自动渲染。它可以完美替代我们的直接使用`Core Graphics`绘制`layer`，对比之下使用`CAShapeLayer`有以下优点：

- 渲染快速。CAShapeLayer使用了硬件加速，绘制同一图形会比用Core Graphics快很多。
- 高效使用内存。一个CAShapeLayer不需要像普通CALayer一样创建一个寄宿图形，所以无论有多大，都不会占用太多的内存。
- 不会被图层边界剪裁掉。
- 不会出现像素化。


所以最终我们的画板程序使用`CAShapeLayer`来实现线条的绘制，性能非常稳定，效果图如下：

![效果图3](http://7xkdhe.com1.z0.glb.clouddn.com/drawRect4.gif)


总结一下绘制性能优化原则：

- 1.绘制图形性能的优化最好的办法就是不去绘制。
- 2.利用专有图层代替绘图需求。
- 3.不得不用到绘图尽量缩小视图面积，并且尽量降低重绘频率。
- 4.异步绘制，推测内容，提前在其他线程绘制图片，在主线程中直接设置图片。


本文最后一个效果图为仿写猿题库练题画板功能，`demo`请在`github`搜索`BHBDrawBoarderDemo`。或者直接[戳这里](https://github.com/bb-coder/BHBDrawBoarderDemo)。

好了，就是这么多，文章比较乱，如有纰漏请不吝指出！想看对本文的补充[请到这里](http://bihongbo.com/2016/01/11/memoryGhostMore/)

good luck！


参考：

iOS Core Animation: Advanced Techniques

感谢：

AttackOnDobby及其翻译团队。

－－－－－－－－－－－－－－－

版权申明：我已将本文在微信公众平台的发表权「独家代理」给 iOS 开发（iOSDevTips）微信公共帐号。
扫码关注「iOS 开发」：![二维码](http://blog.devtang.com/images/weixin-qr.jpg)


    
