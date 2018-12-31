---
title: 用Swift搭建一个简单的web服务器
tags:
- Programming
- Swift
date: 2017-9-8 19:36:00
categories: 
- Programming
---
# 概览
本文介绍了如何用Swift与Perfect来搭建一个简单的HTTP服务器
# 服务器端Swift的框架管理
首先，需要安装Perfect的Perfect Assistant (PA)。
PA帮助我们管理基于Perfect的Swift项目，并可以帮助我们一键部署到服务器。（具体关于PA的使用，请参考我搬运的[Perfect Swift 服务器软件助手v2详细教程](https://www.bilibili.com/video/av12211388/) ☆*:.｡. o(≧▽≦)o .｡.:*☆）

而Perfect是我们今天的主角，目前Swift最厉害的web框架（他们自己是这么说的）

[Perfect官网](https://www.perfect.org)

[PA下载页面](https://www.perfect.org/en/perfect-assistant.html)

安装好之后，打开PA，并双击下图中有蓝色框框按钮来建立一个空白项目

![](/img/7/1.png)

在弹出的窗口中选择项目路径，定义项目名称并下一步

![](/img/7/2.png)

然后稍等片刻，等到路径旁的"Not Vailed SPM"消失后，点开Server一栏，把“HTTPServer”拖到上面空白的Project Dependencies中

![](/img/7/3.png)

![](/img/7/4.png)

至此，本文所需要的依赖已安装好(´▽｀)

# 代码部分
首先，先按一下cmd+s保存一下刚刚的设置，（可能会风火轮，耐心等候即可，再不然请科学上网）然后点击上方的open -> open in project

![](/img/7/5.png)

然后，你就会看到熟悉的Xcode界面。打开main.swift，如下图

![](/img/7/6.png)

紧接着，输入以下代码

```swift
import PerfectLib
import PerfectHTTP
import PerfectHTTPServer

let server = HTTPServer()
server.documentRoot = "/webroot" // 服务器提供静态文件的根路径

// 如果想配置HTTPS服务器，则在这两行配置证书(可选),否则请直接删除这两行
server.ssl = (sslCert: "/certificates/www.tcwq.tech.crt", sslKey: "/certificates/www.tcwq.tech.key")
sslServer.caCert = "/certificates/www.tcwq.tech.ca-bundle"

server.serverPort = 80 // 设置监听端口，一般HTTP为80/8080，HTTPS为443

// 运行服务器
do {
    try server.start()
} catch {
    fatalError(error.localizedDescription)
}

```

然后就好了(((o(*ﾟ▽ﾟ*)o)))在你的documentRoot下放好文件，点击运行

你可以在运行成功后尝试访问
> localhost:你设置的端口号/文件路径

然后你就会看到documentRoot下的文件了♪───Ｏ（≧∇≦）Ｏ────♪