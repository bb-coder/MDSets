layout: p
title: 内存恶鬼drawRect（续:答疑篇）
date: 2016-01-11 03:00:10
category: iOS
tags: iOS
---
###致谢
首先我没有想到前几天写的文章（[内存恶鬼drawRect](http://bihongbo.com/2016/01/03/memoryGhostdrawRect/)）会有这么多人关注，感谢大家的各种肯定和建议，谢谢大家。另外感谢[@唐巧](http://weibo.com/tangqiaoboy)在公众号上发布本文，还要感谢各位同学的转发分享。

###答疑
我的这篇拙作是因为同事辉哥的离职让我想起来去把当初和他解决的比较有意思的问题梳理总结一下，写的并不甚完美，通过公众号的回复和微博的回复私信等等，我收集了读者大大们的问题，对这些问题我会以我的能力一一尝试解答，如果有错误的地方还请不吝指出。

<!-- more -->

####1.内存没有问题啊，为什么我们的代码内存不会暴增？

首先检讨一下，我在原文当中第一个内存暴增例子可能过于简单不够详尽。其实内存的增长与绘图视图的大小和屏幕分辨率有直接关系，你可以尝试将视图范围扩大，并且使用iPhone 6 Plus或者Ipad等高分辨率机型进行测试。由于我们的绘图程序支持两只手指拖动绘图版(很多人忽略了这个功能，导致很多内容存在误解，建议大家用真机玩一下这个功能)，实际的绘图区域要比屏幕大出很多倍，所以内存增长特别明显，这也是我们对`drawRect`使用不好的例子，如果你使用的相当谨慎，内存是不会有很严重的问题(也许只有几MB)，但是我们有能力去优化它，就应该尽量让它运行的更好！另外我在`github`上的`BHBDrawBorderDemo`仓库上传了一个比较明显的暴增例子，使用`drawRect`和`CAShapeLayer`的代码都有，`demo`很简单就是弹出一个视图，在这个视图上画2个矩形如下图，你可以在调试的时候非常方便的切换,代码[戳这里](https://github.com/bb-coder/BHBDrawBoarderDemo/tree/master/DrawBorderBugDemo)

![效果图0](http://7xkdhe.com1.z0.glb.clouddn.com/boke201.jpeg)

另外下面这张图是我自己在不同机型两种方案的对比结果（对比的对象只是屏幕大小的视图，我们的绘图程序会达到300M的内存是因为视图的大小是屏幕的好多倍），你可以亲自拿我的[Demo](https://github.com/bb-coder/BHBDrawBoarderDemo/tree/master/DrawBorderBugDemo)试试。
![效果图1](http://7xkdhe.com1.z0.glb.clouddn.com/boke2.jpeg)

####2.drawRect里面加上super调用行不行？

这个问题我实在不想解释，但是又怕有人误解，只好提一下。

同学，苹果官方文档是这样说的:

__If you subclass UIView directly, your implementation of this method does not need to call super.__

####3.无所谓啊，其实没那么大影响，谈不上什么恶鬼！

好吧，其实我看见这样的回复的时候我就知道，你肯定是个大牛，可是还是有很多人特别是新人一开始并不会注意到这一点，他们可能没有类似的经验去保持警惕。我把标题定的这么吓人也是想给新人朋友们提个醒，`drawRect`用的时候要注意下内存问题。另外我也没有说过让大家弃用`drawRect`,我在文章结尾处已经明确说了，原则是尽量不去绘制，如果不能避免，要尽可能的减小绘制的范围，系统虽然会帮你创建比较合适的范围，也就是说不一定你创建多大的视图，图形上下文就有多大。但是别忘了，只要你设置一下背景颜色，布满`view`的寄宿图就会让你的图形上下文占用内存达到峰值。

####4.drawRect一般在什么场景下用？

其实与问题3一样，尽量别用它，你可以试试我的[Demo](https://github.com/bb-coder/BHBDrawBoarderDemo/tree/master/DrawBorderBugDemo)，就算你在里面什么都不画，它也会占用内存，而且这个内存量还不少。所以，你即使优化的再好，也不如不去绘制。当然了，很多需求用专有图层替代也很麻烦，如果影响不大，而且你对内存的把握很高或者不在乎，你可以直接使用`drawRect`。

####5.`drawRect`可以用算法来优化，`CAShapeLayer`性能也不是最优的。

首先感谢两位朋友的指教，恕笔者才疏学浅，笔者回头查了很久也没有查到相关的资料或者博客，比`CAShapeLayer`性能还要好的我也就能想到OpenGLES也不知道对不对，比较好奇，如果不忙加我微博[@毕洪博](http://weibo.com/u/5578532822?is_all=1)咱们联系讨论一下～～


####6.appending...

主要的问题就这么多，本文会持续更新，只要有问题我就会追加上来，大家如果有好的优化思路赶紧告知我呀～也欢迎各路神通广大之人来调戏我！！！流氓这么少～～哎，天色已晚，睡了...

good luck!






	



