---
title: 在Swift中食用可选值的特殊技巧？
date: 2017-08-20 11:13:56
tags: 
- Programming
- iOS Development
- Swift
categories: Programming
---
> 在Swift中使用可选值的「高端操作」

# 强制解包
很多时候当我们确定某个值一定不为nil时，会使用“!”来强制解包。
然后可能就会见到：
![喜闻乐见的Unexpectedly found nil](/img/4/foundNil.png)

最近在读objc.io的「Advanced Swift」,看到了一个可以对强制解包进行改进的操作

## 改进错误信息
可以通过定义一个新的运算符“!!” 来在强制解包发现nil时打印自定义错误信息。
```swift
infix operator !!

func !! <T>(wrapped: T?, failureText: @autoclosure () -> String) -> T {
    if let unwrapped = wrapped {
        return unwrapped
    }
    fatalError(failureText())
}
```
其中**@autoclosure**表示自动闭包，可以让failureText只在需要的时候被执行

使用起来类似于这样：
```swift
let a = "Captain"
let b = Int(a) !! "Var a:\(a) cannot be converted to an Int"
```

# 使用断言（在调试版本中）
> 在最终发布版本中选择让程序崩溃是很大胆的行为

因为你可能会因此遭遇

![Daily rejection](/img/4/rejected.png)

或者

![Terrible rating](/img/4/rating.png)

(同样是来自「Advanced Swift」的高端操作)
可以通过定义"!?"运算符来在调试时使用断言，而在发布时返回默认值（如果值为nil）
```swift
infix operator !?

func !? <T: ExpressibleByArrayLiteral>(wrapped: T?, failureText: @autoclosure () -> String) -> T {
    assert(wrapped == nil, failureText())
    return wrapped ?? []
}

func !? <T: ExpressibleByStringLiteral>(wrapped: T?, failureText: @autoclosure () -> String) -> T {
    assert(wrapped == nil, failureText())
    return wrapped ?? ""
}

// ...... Other types
```
使用如下：
```swift
let a = "six"
print(Int(a) !? "a is not able to be converted to an Int")
```