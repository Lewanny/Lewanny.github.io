---
title: iOS 上传 ipa 包至 App Store 报错 Error ITMS-90096 Your binary is not optimized for iPhone 5
date: 2015-11-06 12:35:26
tags: AppStore 上传
categories: Objective-C
---

### 问题描述

向 App Store 提交 ipa 包时报如下错误:

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-upload-appstore-error-90096-solution/error90096.png" width ="500" height="520"></img>

使用 Xcode7 向 App Stor e提交 ipa 包时，提示 `ERROR ITMS-90096：”You binary is not optimized for iPhone 5….”` 错误。

<!--more-->

### 解决方案
解决方案在于对 iPhone5 单独设置 LaunchImage 图片:

- 把对应的图片放在工程的`根目录下`。
- 图片的尺寸需保证为 `320*568 大小`。
- 图片的命名为 `Default-568.png` 。

以上三点为`必要条件`，然后在工程的 plist 文件中添加 UILaunchimages 数组即可，如下图：

<img src="http://omkug1guu.bkt.clouddn.com/iOS-upload-appstore-error-90096-solution/plistSet.png" width ="480" height="220"></img>
