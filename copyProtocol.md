title: "copy协议解析" 
date: 2015-07-16 13:27:04 
categories: iOS 
tags: iOS 
---

# copy协议解析
---

在日常的 `iOS` 开发中，我们经常会用到如下代码：

    @property (nonatomic,copy) NSString * name;

&emsp;&emsp;很多人告诉你只要是 `NSString` 类型你就必须给我用 `copy` 来声明。但是如果我告诉你很多时候 `NSDictionary` 或者 `NSArray` 等等也有可能用 `copy` 来修饰的话，你是不是会独自混乱一会儿？所以如果你想知道这其中的原理请往下看，如果你只是停留在简单的使用层面，那么请去其他地方看更有意义的事情吧。

<!-- more -->

&emsp;&emsp;首先要说的是，使用 `copy` 的目的很简单，就是复制一个对象本身，而不是创建一个指向这个对象的指针。那么我们来看看如果上面的代码使用strong会发生什么事情。

    @property (nonatomic,strong) NSString * name;

在主函数中我们编写如下代码：

    NSMutableString * str = [NSMutableString stringWithString:@"Tom"];

    self.name = str;

    NSLog(@"str:%@--self.name:%@",str,self.name);

    [str appendString:@" and jack"];//追加字符串

    NSLog(@"str:%@--self.name:%@",str,self.name);

&emsp;&emsp;请注意这里我不使用 `NSString` 而使用 `NSMutableString` 是因为像 `@“123”` 这样的字符串苹果为了性能考虑都变成了常量，并且使用 `NSMutableString` 我可以有效的利用字符串的改变来描述使用 `strong` 与 `copy` 的不同。
&emsp;&emsp;上述代码中，变量 `str` 经过追加字符串操作后显然应该变成 `@"Tom and jack"` ，让我们来看一下运行结果：
![strong运行结果][1]
&emsp;&emsp;细心的同学可以一下子就发现问题： `str` 和 `self.name` 竟然输出的是一样的，但是貌似我们并没有改变 `self.name` 。这就说明 `str` 和 `self.name` 指向的是同一块内存地址，这种情况在实际的开发中存在很多隐患。如果我们使用的是 `copy` ，输出的结果如下图：
![copy运行结果][2]
&emsp;&emsp;所以当我们需要保证属性是独立复制出来的一个新对象的时候，请使用 `copy` ，如果只是简单的指针传递，请使用 `strong` 。
&emsp;&emsp;直到现在我并没有提及到 `copy` 协议的任何东西，不要着急，下面我们来自己定义一个类。

    @interface Animal : NSObject

    @property (nonatomic,copy) NSString * name;

    @end

&emsp;&emsp;这个类继承自 `NSObject` 并仅有一个属性 `name` 。这时我们在主控制器中做一个 `Animal` 的属性,并且我们希望它是 `copy` 的，并编写主函数代码。

    @property (nonatomic,copy) Animal   * dog;

    Animal * a1 = [[Animal alloc]init];

    a1.name = @"Tom";

    self.dog = a1;

    NSLog(@"a1Name:%@--self.dogName:%@",a1.name,self.dog.name);

    a1.name = @"Jack";

    NSLog(@"a1Name:%@--self.dogName:%@",a1.name,self.dog.name);

&emsp;&emsp;让我们直接运行：
![自定义类运行结果][3]
&emsp;&emsp;很不幸，程序崩溃了，崩溃的原因写的很清楚 `Animal` 类缺少一个方法 `copyWithZone:` ，这个方法就是我们要说的 `copy` 协议方法，让我们来看看官方头文件的说明：

    @protocol NSCopying

    - (id)copyWithZone:(NSZone *)zone;

    @end

    @protocol NSMutableCopying

    - (id)mutableCopyWithZone:(NSZone *)zone;

    @end

遵守 `NSCopying` 协议可以使用 `[anyObejct copy]` 这样的方法来拷贝一个对象，遵守 `NSMutableCopying` 可以使用 `[anyObejct mutableCopy]` 来拷贝出一个可变对象。我们暂时先看 `NSCopying` 协议，在 `Animal` 中我们补充这个协议方法：

    @interface Animal : NSObject<NSCopying>

    -(id)copyWithZone:(NSZone *)zone
    {
    return [[[self class] allocWithZone:zone] initWithName:self.name];
    }

    -(instancetype)initWithName:(NSString *) name
    {
    if (self = [super init]) {
    self.name = name;
    }
    return self;
    }

再次运行：
![正确运行结果][4]
结果显示正确,这说明系统的很多类如 `NSArray` 、 `NSDictionary` 、 `NSString` 等都默认实现了 `copy` 协议，而我们自己定义的类如果想要拷贝功能就必须自己实现它。

现在我们来看 `NSMutableCopying` 协议，顾名思义我们利用这个协议可以得到一个可变拷贝对象，如果你之前对 `NSArray` 与 `NSMutableArray` 了如指掌的话我就不用说太多了，直接看代码。

    @property (nonatomic,copy) NSMutableArray * testArr;

    //主程序中
    NSMutableArray * arr1 = [[NSMutableArray alloc]init];

    [arr1 addObject:@"haha"];
    [arr1 addObject:@"nihao"];

    self.testArr = arr1;

    NSLog(@"%@---%@",arr1,self.testArr);

    [arr1 setObject:@"hehe" atIndexedSubscript:0];

    NSLog(@"%@---%@",arr1,self.testArr);

结果如下：
![mutableCopy运行结果][5]

但是当我们改变 `self.testArr` 时

    [self.testArr setObject:@"123" atIndexedSubscript:0];

会得到如下结果：
![崩溃结果][6]
崩溃了，此时我们输出 `self.testArr` 看看：
![self.testArr类型][7]
我们从一个 `NSMutableArray` 赋值过来的属性竟然是 `NSArray` 类型的，那么我们很自然的会想到 `NSMutableCopying` 协议，下面我们来重写 `self.testArr` 的 `setter` 方法。

    - (void)setTestArr:(NSMutableArray *)testArr
    {
    _testArr = [testArr mutableCopy];
    }

再次运行：
![正确的结果][8]
程序没有崩溃，并且得到了一个 `NSMutableArray` 类型的属性，并成功改变了数组的第一个元素。

以上是我对 `copy` 协议的一点拙见,代码我就不贴了，大家敲敲练习一下。

good luck!


[1]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage1.png
[2]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage2.png
[3]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage3.png
[4]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage4.png
[5]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage5.png
[6]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage6.png
[7]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage7.png
[8]: http://7xkdhe.com1.z0.glb.clouddn.com/copyImage8.png
