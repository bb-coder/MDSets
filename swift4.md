title: "Swift Section Four(函数与闭包)" 
date: 2014-10-23 14:20:01 
categories: swift 
tags: swift 
---

# 函数与闭包
---

```obj-c

    //: Playground - noun: a place where people can play

    import UIKit

    //swift 第四课 函数与闭包

    //1.函数的用法
    func greet(name: String, day: String) ->String {

    return "Hello \(name), today is \(day)"

    }

    greet("Bob","Tuesday")


    //2.多返回值的函数
    func calculateStatistics(scores: [Int]) ->(min: Int, max: Int, sum: Int){

    var min = scores[0]

    var max = scores[0]

    var sum = 0

    for score in scores{

    if score > max {

    max = score

    }else if score < min{

    min = score

    }

    sum += score

    }

    return (min, max, sum)

    }

    let statistics = calculateStatistics([5, 3, 100, 3, 9])

    print(statistics.sum)

    print(statistics.2)

    //3.不限参数个数的函数
    func sumOf(numbers: Int...) -> Int {

    var sum = 0

    for number in numbers{

    sum += number

    }

    return sum

    }

    sumOf()

    sumOf(42, 597, 12)

    //4.嵌套函数
    func returnFifteen() -> Int{

    var y = 10

    func add(){

    y += 5

    }

    add()

    return y

    }

    returnFifteen()

    //5.函数可以作为函数的返回值
    func makeIncrementer() -> (Int -> Int){

    func addOne(number: Int) -> Int {

    return 1 + number

    }

    return addOne

    }

    var increment = makeIncrementer()

    increment(7)

    //5.函数也可以作为函数的参数
    func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {

    for item in list {

    if condition(item) {

    return true

    }

    }

    return false

    }

    func lessThanTen (number: Int) -> Bool {

    return number < 10

    }

    var numbers = [20, 19, 5, 12]

    hasAnyMatches(numbers, lessThanTen)


    //6.闭包的使用
    //首先介绍一下map方法的定义：返回一个数组，这个数组内的元素是原数组经过闭包函数计算返回的值

    numbers.map({

    (number: Int) -> Int in

    let result = 3 * number

    return result

    })

    //简写方式
    let mappedNumbers = numbers.map({

    number in 3 * number

    })

    print(mappedNumbers)

    //利用闭包排序
    let sortedNumbers = numbers.sorted {

    $0 > $1

    }

    print(sortedNumbers)

```

源代码请前往[我的github][1]

[1]:https://github.com/bb-coder/swift_Session