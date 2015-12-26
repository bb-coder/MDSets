title: "IOS开发中ARC下的assign和weak区别"
date: 2014-05-20 09:55:31
categories: iOS
description: 自从 iOS 的内存管理出了 ARC 之后，strong 与 weak 横空出世，在某些时候 weak 和 assign 很像但是又有不得不注意的地方。
tags: iOS
---

在ARC中的 `assign` 和 `weak` 可以说非常相像，导致有很多人误以为他们是一摸一样的，在任何时候都可以划等价，但事实却不是这样。

<!-- more -->

在群里，有人问，`id` 类型的 `delegate` 属性到底是用 `assign` 还是 `weak` 

    @property (weak, nonatomic) id<AnyDelegate> delegate;
    @property (assign, nonatomic) id<AnyDelegate> delegate;

大家众说纷纭，说都可以的，说 `assign` 的，说 `weak` 的都有，下面我们来看一本书中的描述：
>“The main difference between weak and assign is that the with weak, once the object being pointed to is no longer valid, the pointer is nilled out. Assigning the pointer the value nil avoids many crashes as messages sent to nil are essentially no-ops”

>Excerpt From: Daniel H Steinberg.

>“Developing iOS 7 Apps for iPad and iPhone.” Dim Sum Thinking, Inc, 2014. iBooks. iTunes - Books

大致的意思是说， `weak` 比 `assign` 多了一个功能就是当属性所指向的对象消失的时候（也就是内存引用计数为0）会自动赋值为 `nil` ，这样再向 `weak` 修饰的属性发送消息就不会导致野指针操作crash。

可能不太好理解下面我写了一个演示程序。

OC：

    //
    //  ViewController.m
    //  weak与assgin的区别
    //
    //  Created by bihongbo on 14/5/20.
    //  Copyright (c) 2015年 毕洪博. All rights reserved.
    //

    #import "ViewController.h"

    @interface ViewController ()

    @property (nonatomic,weak) id      weakPoint;

    @property (nonatomic,assign) id    assignPoint;

    @property (nonatomic,strong) id    strongPoint;

    @end

    @implementation ViewController

    - (void)viewDidLoad {
    [super viewDidLoad];
    self.strongPoint = [NSDate date];
    NSLog(@"strong属性：%@",self.strongPoint);
    self.weakPoint = self.strongPoint;
    self.assignPoint = self.strongPoint;
    self.strongPoint = nil;
    NSLog(@"weak属性：%@",self.weakPoint);
    //    NSLog(@"assign属性：%@",self.assignPoint);
    }

    @end


当程序中的注释被打开时，运行程序有可能会崩溃（有时候不崩溃，你可能需要多运行几次），这是因为当 `assign` 指针所指向的内存被释放（释放并不等于抹除，只是引用计数为0），不会自动赋值 `nil` ，这样再引用 `self.assignPoint` 就会导致野指针操作，如果这个操作发生时内存还没有改变内容，依旧可以输出正确的结果，而如果发生时内存内容被改变了，就会crash。

###结论：在 `ARC` 模式下编程时，指针变量一定要用 `weak` 修饰，只有基本数据类型和结构体需要用 `assgin` ，例如 `delegate` ，一定要用 `weak` 修饰。

goodluck！

---




