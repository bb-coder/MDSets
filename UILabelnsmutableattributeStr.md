title: "UILabel的文字排版特效，删除线，下划线等"
date: 2014-06-13 15:46:21
categories: iOS
description: iOS开发中文字排版的很多常用效果，比如下划线，删除线等...本文所用代码为 swift 版本，OC 同理。
tags: [iOS,文字排版,UILabel]
---


##首先要做文字排版的特效，需要用到 `iOS` 系统自带的一个 `Foundation` 框架下的 `NSMutableAttributeString` .步骤如下：


###1.创建属性字串
```obj-c
    var mAttributeStr:NSMutableAttributedString = NSMutableAttributedString(string: "$¥12345")
```
###2.添加属性

```obj-c
    mAttributeStr.addAttribute(NSStrikethroughStyleAttributeName,   value:NSUnderlineStyle.StyleSingle.rawValue, range:     NSRange(location: 2, length: 5))
    mAttributeStr.addAttribute(NSStrikethroughStyleAttributeName, value:NSUnderlineStyle.StyleNone.rawValue, range: NSRange(location: 0, length: 2))
```
###3.赋值给UILabel

```obj-c
    strikedLabel.attributedText = mAttributeStr
```

##简单3步就创建好了字符串的删除线效果，更多效果请参考[我的demo][1].

[1]:https://github.com/bb-coder/swift_add_strikethrough

---