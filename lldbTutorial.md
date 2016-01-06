## Xcode开发lldb debugger最全讲解
所谓工欲善其事必先利其器，这篇博客的内容是我在使用Xcode进行iOS开发所使用的一个洋枪大炮：[lldb](https://en.wikipedia.org/wiki/LLDB_\(debugger\))

关于`lldb`我相信你看这一篇就够了,本次我收录了大部分我在开发中常用的`lldb`命令，因为`lldb`支持`python`编写的脚本插件，`facebook`出了一款更加方便的脚本工具`chisel`在后面我也会着重介绍。如果你有时间，你可通读本文，本文会在`lldb`外介绍一些常用的调试方法，让你在iOS开发当中尽量避免重复运行、过度的`NSLog`。如果你是个老炮儿只是忘了某个命令，请按需戳目录。

###目录
* [基础篇](##基础篇)
	* [1.打印](###1.打印)
	* [2.表达式](###2.表达式)
	* [3.镜像](###3.镜像)
	* [4.符号断点](###4.符号断点)
	* [5.帮助](###5.帮助)
* [高级篇](##高级篇)
	* [1.编辑断点](###1.编辑断点)
	* [2.chisel插件](###2.chisel插件)
		* [2.1 chisel的安装](####2.1`chisel`的安装)
		* [2.2 视图层级,控制器层级](####2.2视图层级,控制器层级)
		* [2.3 视图添加取消边框](####2.3视图添加取消边框)
		* [2.4 对象内部结构](####2.4对象内部结构)
		* [2.5 消息传递链](####2.5消息传递链)
		* [2.6 预览截图](####2.6预览截图)
		* [2.7 对象继成关系](####2.7对象继成关系)
		* [2.8 模拟点击，抓取点击视图](####2.8模拟点击，抓取点击视图)
		* [2.9隐藏显示对象](####2.9隐藏显示对象)
		* [2.10消息断点](####2.10消息断点)
		* [2.11帮助](####2.11帮助)
	* [3.控制台lldb调试](###3.控制台lldb调试)
		* [3.1 链接程序](####3.1链接程序)
	

##基础篇
###1.打印

`print`,`p`,`exp --`

打印基本数据类型
	
`po` ,`"exp -0 --"`


格式的完整清单可以参考[Output Formats](https://sourceware.org/gdb/onlinedocs/gdb/Output-Formats.html)。

###2.表达式
`expression`

使用可以参考[Evaluating Expressions](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-command-examples.html#//apple_ref/doc/uid/TP40012917-CH3-SW5)。

###3.镜像
`image`

`image list`

`image lookup`

`image lookup --address 0x00000`

`image lookup --type anyobject`

具体可以参考[Executable and Shared Library Query Commands](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-command-examples.html#//apple_ref/doc/uid/TP40012917-CH3-SW5)。

###4.符号断点

`Add symbolic breakpoint`

###5.帮助
`help`

`apropos`


##高级篇

###1.编辑断点

1.首先注释以前那该死的log背景被点击了，然后我加一个优美的断点。

2.然后右键点击断点编辑断点，首先选择Action,logMessage，再点击+,选择DebugerCommander，然后这里面的命令和右下角的lldb命令是完全一样的，这个命令你随意打。

3.最重要的一步，一点要选择automaticlly continue after evalating value，这个可以使得程序不会中断继续执行。

###2.chisel插件

####2.1`chisel`的安装

####2.2视图层级,控制器层级

`pviews`,`pvc`

####2.3视图添加取消边框

`border` , `unborder`

####2.4对象内部结构

`pinternals`

####2.5消息传递链

`presponder`

####2.6预览截图

`visualize`

####2.7对象继成关系

`pclass`

####2.8模拟点击，抓取点击视图

`taplog`

####2.9隐藏显示对象

`hide`,`show`

####2.10消息断点

`bmessage`

####2.11帮助

`help xxx`

###3.控制台lldb调试
####3.1链接程序
...




http://www.cimgf.com/2012/12/13/xcode-lldb-tutorial/



