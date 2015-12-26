title: "Swift Section Three(分支和循环)" 
date: 2014-10-20 13:11:05 
categories: swift 
tags: swift 
---

# 分支与循环
---

    // Playground - noun: a place where people can play

    import UIKit

    //Swift 第三课 分支与循环

    //1.if else 与 for in 循环

    let individualScores = [75, 43, 103, 87, 12]

    var teamScore = 0

    for score in individualScores {

    if score > 50 {

    teamScore += 3;

    } else {

    teamScore += 1;

    }

    }

    print(teamScore)

    //2.用"?"来声明可选变量

    var optionalString: String? = "Hello"

    print(optionalString == nil)

    var optionalName: String? = "John Appleseed"

    var greeting = "Hello!"

    //3.用 if let 来进行快速判断可选变量是否为空

    if let name = optionalName {

    greeting = "Hello, \(name)"

    }

    //print(name) 请记住name并没有声明变量，它只在if let 作用域内有效

    //4.switch用法

    let vegetable = "red pepper"

    switch vegetable {

    case "celery":

    let vegetableComment = "Add some raisins and make ants on a log."

    case "cucumber","watercress":

    let vegetableComment = "That would make a good tea sandwich."

    case let x where x.hasSuffix("pepper"):

    let vegetableComment = "Is it a spicy \(x)?"

    default:

    let vegetableComment = "Everything tastes good in soup."

    }


    //5.利用for in 快速遍历字典

    let interestingNumbers = [

    "Prime": [2, 3, 5, 7, 11, 13],

    "Fibonacci": [1, 1, 2, 3, 5, 8],

    "Square": [1, 4, 9, 16, 25]

    ]

    var largest = 0

    for (kind, numbers) in interestingNumbers{

    for number in numbers{

    if number > largest {

    largest = number

    }

    }

    }

    print(largest)


    //6.while repeat循环

    var n = 2

    while n < 100{

    n = n * 2

    }

    print(n)


    //var m = 2  //repeat这个语法可能被cut掉了，现在不能使用
    //
    //repeat{
    //    
    //    m = m * 2
    //    
    //}while m < 100
    //
    //print(m)

    //6. for 的简便写法

    var firstForLoop = 0

    for i in 0..<4 {

    firstForLoop += i

    }

    print(firstForLoop)


    var secondForLoop = 0

    for var i = 0; i < 4; ++i {

    secondForLoop += i

    }

    print(secondForLoop)


源代码请前往[我的github][1]

[1]:https://github.com/bb-coder/swift_Session