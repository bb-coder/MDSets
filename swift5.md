title: "Swift Section Five(对象与类)" 
date: 2014-11-15 16:22:03 
categories: swift 
tags: swift 
---

# 对象与类
---

```obj-c

    //: Playground - noun: a place where people can play

    import UIKit

    //swift 第五课 对象与类

    //1.类的创建方法

    class Shape {

    var numberOfSides = 0//属性的声明与变量常量的声明方式一致

    let numberOfBadges = 1

    var id: Int?

    func simpleDescription() ->String {//方法同样一致

    return "A shape with \(numberOfSides) sides."

    }

    func setupShapeId(id: Int) {

    self.id = id

    }

    }

    var shape = Shape()

    shape.numberOfSides = 7

    var description = shape.simpleDescription()

    shape.setupShapeId(1993)

    //2.类的初始化方法

    class NamedShape {

    var numberOfSides: Int = 0

    var name: String

    init(name: String){

    self.name = name

    }

    func simpleDescription() ->String {

    return "A shape with \(numberOfSides) sides."

    }

    }

```

源代码请前往[我的github][1]

[1]:https://github.com/bb-coder/swift_Session