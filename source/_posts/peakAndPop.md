---
title: 记 Swift4 + Xcode9 中Peak&Pop特性的食用方法
date: 2017-11-25 16:35:00
tags: 
- Programming
- iOS Development
categories: Programming
---
> 前两天想在[BirthReminder](https://captainyukinoshitahachiman.github.io/BirthReminder/)里加上Peak&Pop的feature，结果找到的博文全是OC的，苹果的文档又贼鸡儿不好用，踩了一堆坑。今天整理一下，以警示后人（

# 效果预览
![](/img/11/demo.gif)

# 正片
## 从TableViewCell进行Peak&Pop
首先,你需要让你的VC遵循UIViewControllerPreviewingDelegate协议，以下拿BirthReminder为例子
```swift
extension IndexViewController: UIViewControllerPreviewingDelegate {
    
    func previewingContext(_ previewingContext: UIViewControllerPreviewing, viewControllerForLocation location: CGPoint) -> UIViewController? {
    	// not implemented
    }
    
    func previewingContext(_ previewingContext: UIViewControllerPreviewing, commit viewControllerToCommit: UIViewController) {
    	// not implemented
    }
    
}
```
接下来，为你的TableViewCell注册previewing
```swift
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let index = indexPath.row
        let cellData = data[index]
        let cell = tableView.dequeueReusableCell(withIdentifier: "personalCell", for: indexPath) as! PersonalCell
        
        // cell implementation
        cell.nameLabel.text = cellData.name
        cell.birthLabel.text = status ? cellData.birth.toLocalizedDate() : cellData.birth.toLeftDays()
        if let imgData = cellData.picData {
            cell.picView.image = UIImage(data: imgData)
        } else {
            cell.picView.image = UIImage(image: UIImage(), scaledTo: CGSize(width: 100, height: 100))
        }
        
        // register for previewing if the device supports 3D Touch
        if traitCollection.forceTouchCapability == .available { 
            registerForPreviewing(with: self, sourceView: cell)
        }
        
        return cell
    }
```
这样，当你用力按压cell时，就会调用UIViewControllerPreviewingDelegate的这两个方法
```swift
func previewingContext(_ previewingContext: UIViewControllerPreviewing, viewControllerForLocation location: CGPoint) -> UIViewController?
    
func previewingContext(_ previewingContext: UIViewControllerPreviewing, commit viewControllerToCommit: UIViewController)
```
这里，我们要对两个方法进行完善。
对于第一个，是提供你想要弹出的Controller。(若返回nil则表示取消此次Peak&Pop)

首先，我们通过
```swift
previewingContext.sourceView
```
来获取到被按下的cell，然后通过
```swift
tableView.indexPath(for: UITableViewCell)
```
来获取cell的indexPath，如下：
```swift
    func previewingContext(_ previewingContext: UIViewControllerPreviewing, viewControllerForLocation location: CGPoint) -> UIViewController? {
    	// get the touched cell
        if let cell = previewingContext.sourceView as? UITableViewCell {
        	// get the indexPath of the cell
            let indexPath = tableView.indexPath(for: cell)!
            
            // return the previewing controller
            let storyBoard = UIStoryboard(name: "Main", bundle: nil)
            if let controller = storyBoard.instantiateViewController(withIdentifier: "birthCard") as? BirthCardController{
                let person = data[indexPath.row]
                controller.person = person
                return controller
            } else {
                return nil
            }
            
        } else {
        	return nil
        }
    }
```

至于第二个方法，（我觉得）没什么好讲的（其实是我懒），这里的实现如下
```swift
    func previewingContext(_ previewingContext: UIViewControllerPreviewing, commit viewControllerToCommit: UIViewController) {
        viewControllerToCommit.hidesBottomBarWhenPushed = true
        show(viewControllerToCommit, sender: nil)
    }
```

这时，你就已经基本掌握PeakAndPop了 (((o(*ﾟ▽ﾟ*)o)))

## PreviewActions
QuickAction就是你上拉时看到的几个选项。修改起来很简单，只需要override **被弹出的ViewController** 的 previewActionItems 属性即可
```swift
    override var previewActionItems: [UIPreviewActionItem] {
        return [
        	// here add your quick actions
        ]
    }
```
这里请注意，返回的是UIPreviewAction，而不是它的父类UIPreviewActionItem

至于UIPreviewAction，非常简单，相信看了init方法就能明白：
```swift
init(title: String, style: UIPreviewActionStyle, handler: (UIPreviewAction, UIViewController) -> Void)
```
顺便，handler里的controller是被弹出的controller，在这里也就是self，所以我有点不明白Apple怎么想的，还请大佬们指点（

到这里，你就已经会（基本地）使用Peak&Pop了 （散花）