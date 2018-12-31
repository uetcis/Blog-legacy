---
title: 记 iOS 10.3+ 中请求评分的正确姿势
date: 2017-10-07 13:50:00
tags: 
- Programming
- iOS Development
categories: Programming
---
> 要说为什么会有本文也是一个挺有意思的故事。前些天我用小猿搜题看几何题的时候突然蹦出了一个评分窗口让我打分，查了查才发现是StoreKit里的新操作23333

过去我们开发iOS App诱导用户评分主要是通过跳转到App Store的方式，而在iOS 10.3中这个操作被Apple禁止了，替代它的是StoreKit中的SKStoreReviewController，看起来是这样的
![](http://api.cocoachina.com/uploads/20170814/1502676856824262.jpg)
很明显用户体验不知道比以前好到哪里去了

下面我们就通过一个案例来讲一讲这玩意儿咋用

# 案例
我们先打开一个以前的项目,其中原先跳转的代码是这样的
```swift
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        if indexPath.row == 3 {
            guard let url = URL(string : "itms-apps://itunes.apple.com/app/id1205136568") else {
                print("wtf?!")
                return
            }
            guard #available(iOS 10, *) else {
                UIApplication.shared.openURL(url)
                return
            }
            UIApplication.shared.open(url, options: [:], completionHandler: nil)
        }
    }
```
因为在10.3开始Apple禁止了跳转App Store来评分的行为，所以如果系统是10.3或更高，我们要在这里不做任何操作，即把上面的代码改为
```swift
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        if indexPath.row == 3 {
            if #available(iOS 10.3, *) {
                return
            }
            guard let url = URL(string : "itms-apps://itunes.apple.com/app/id1205136568") else {
                print("wtf?!")
                return
            }
            guard #available(iOS 10, *) else {
                UIApplication.shared.openURL(url)
                return
            }
            UIApplication.shared.open(url, options: [:], completionHandler: nil)
        }
    }
```
同时我们需要隐藏这个cell，以免对用户进行误导
```swift
    override func viewDidAppear(_ animated: Bool) {
        if #available(iOS 10.3, *) {
            tableView.cellForRow(at: IndexPath(row: 3, section: 0))?.isHidden = true
        }
    }
```
你可能会问为什么不直接在判断是10.3系统时使用新API，而是将其隐藏，实际上是有原因的（划重点）

**根据[Apple 官方文档](https://developer.apple.com/documentation/storekit/skstorereviewcontroller/2851536-requestreview)的说法，你每次请求调用SKStoreReviewController时，系统会自行判断要不要给用户展示这个评分栏，而不是每次都可以**

所以如果你点击了评分按钮，而系统又决定不展示这个评分View，气氛就会变得十分尴尬，所以我们应在新系统中隐藏Rate Button，并尝试在用户心情好的时候请求评分。

比如在这个例子中，是一个[拾色器App](https://github.com/LiulietLee/Pick-Color)，那么我们可以在用户选取颜色或保存颜色时请求评分。
实际实现很简单，只需要在适当地方加入以下代码就好了。
```swift
import StoreKit
SKStoreReviewController.requestReview()
```
比如在这个案例中，我们可以在用户选择保存颜色时进行评分请求
```swift
func saveButtonTapped() {
	if #available(iOS 10.3, *) {
		SKStoreReviewController.requestReview()
	}
	defer { goBack() }
	guard let title = textField.text?.trimmingCharacters(in: .whitespacesAndNewlines) else { return }
		if title != "" {
		if let item = item {
        	item.title = title
            model.saveEditedColor()
            delegate?.colorEdited(color: item)
		} else {
			let colorItem = model.saveNewColor(color, title: title)
            delegate?.colorSaved(color: colorItem)
		}
	}
}
```
主要就这些了，代码很简单，但是一定要注意这个View不是你想弹出就弹出的，所以一定要提前想好怎么设计交互。