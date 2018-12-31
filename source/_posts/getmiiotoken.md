---
title: 使用 iOS 设备获取小米智能家居的 Token
date: 2018-08-27 17:55:16
tags: Tech
categories: Tech
---
> 使用小米智能家居时，有时会需要使用设备的 Token，如 [homebridge-mi-philips-light](https://github.com/YinHangCode/homebridge-mi-philips-light)
> 而可以轻易找到的获取 Token 的方法不是需要使用 Android 设备，就是不稳定。对于只持有 iOS 设备的用户极不友好。
> 经过我的一番检索，查到了使用 iOS 设备获取 Token 的方法。

# 使用 iOS 设备获取小米智能家居的 Token

## 1.准备工作
使用你的 iOS 设备在米家 app 中登录账号并添加好需要获取 token 的设备。之后，使用你的电脑备份该设备。

## 2.获取备份中的数据库文件
安装 *iBackup Viewer*,并使用它打开你之前的备份。点击最后一个文件系统树状图标，并找到 AppDomain-com.xiaomi.mihome。在其中找到类似于 1439707687_mihome.sqlite 的文件 （**不是 _mihome.sqlite**），选择它并将其导出。

## 3.获取 Token
首先安装 [python-miio](https://python-miio.readthedocs.io/en/latest/discovery.html#installation):
` pip3 install python-miio`

之后，运行 `miio-extract-tokens path-to-the-sqlite-file`,你会得到类似于下面的结果：
```
INFO:miio.extract_tokens:Reading database from path-to-the-sqlite-file
INFO:miio.extract_tokens:Reading tokens from Apple DB
Gateway
	Model: lumi.gateway.v3
	IP address: // IP
	Token: // Token
	MAC: // MAC
Philips connected ceiling
	Model: philips.light.ceiling
	IP address: // IP
	Token: // Token
	MAC: // MAC
AC Controller
	Model: lumi.acpartner.v2
	IP address: // IP
	Token: // Token
	MAC: // MAC
Philips EyeCare connected desk lamp gen2.
	Model: philips.light.sread1
	IP address: // IP
	Token: // Token
	MAC: // MAC
```
