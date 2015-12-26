## Xcode开发lldb debugger最全讲解

###1.打印

`print`,`p`,`exp --`

	
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


##更高级的用法

###1.断点输出

1.首先注释以前那该死的log背景被点击了，然后我加一个优美的断点。

2.然后右键点击断点编辑断点，首先选择Action,logMessage，再点击+,选择DebugerCommander，然后这里面的命令和右下角的lldb命令是完全一样的，这个命令你随意打。

3.最重要的一步，一点要选择automaticlly continue after evalating value，这个可以使得程序不会中断继续执行。

###2.chisel插件

####2.1 `chisel`的安装

####2.2 视图层级,控制器层级

`pviews`,`pvc`

####2.3 视图添加取消边框

`border` , `unborder`

####2.4 对象内部结构

`pinternals`

####2.5 消息传递链

`presponder`

####2.6 预览截图

`visualize`

####2.7 对象继成关系

`pclass`

####2.8 模拟点击，抓取点击视图

`taplog`

####2.9 隐藏显示对象

`hide`,`show`

####2.10 消息断点

`bmessage`

####2.11 帮助

`help xxx`

###3.控制台lldb调试
####3.1 链接程序
...





