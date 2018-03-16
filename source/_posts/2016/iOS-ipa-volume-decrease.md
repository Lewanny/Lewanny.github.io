---
title: iOS 如何优化 ipa 程序安装包的大小
date: 2016-05-17 22:34:54
tags: ipa 优化
categories: Objective-C
---

iOS 开发中最终需要的结果便是 ipa 应用包，ipa 应用包自然也是越小越好，下面介绍了 iOS 优化 ipa 安装包大小的几种方法。

### 配置编译选项 Genetate Debug Symbols 设置为 NO
`Generate Debug Symbols` 这个设置在 DEBUG 和 RELEASE下 均默认为 YES。
当 Generate Debug Symbols 设置为 YES 时，编译产生的 .o 文件会大一些，当然最终生成的可执行文件也大一些。
当 Generate Debug Symbols 设置为 NO 的时候，在 Xcode 中设置的断点不会中断，同样生成的 ipa 安装包也会小一些。

<!--more-->

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-ipa-volume-decrease/GenetateDebugSymbols.png" width=800></img>
  
  
### 适当舍弃架构 armv7
因为 armv7 用于支持 4s 和 3gs ，4s 是 2011 年 11 月正式上线，虽然还有小部分人在使用，如果是是追求包体大小的完全可以舍弃了。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-ipa-volume-decrease/armv7.png" width=800></img>
  
  
### 删除无用的图片音频和视频文件
ipa 包的体积增大很大程度上取决于资源文件的大小。包括 Images.xcassets 中无用的图片， bundle 中的音频、视频、图片 和字体文件等。


### Optimization Level 等编译项优化
1. Build Settings -> Optimization Level 有几个编译优化选项，release 版应该选择 Fastest, Smalllest ，这个选项会开启那些不增加代码大小的全部优化，并让可执行文件尽可能小。

2. Strip Linked Product / Deployment Postprocessing / Symbols Hidden by Default 在 release 版本应该设为 YES ，可以去除不必要的调试符号。Symbols Hidden by Default 会把所有符号都定义成 ”private extern” 。

> 这些选项目前都是 XCode 里 release 的默认选项，但旧版 XCode 生成的项目可能不是，可以检查一下。


### 附: 如何查看 ipa 包中的大文件
1. 找到自己打包后的 ipa ，然后右键，打开方式选择归档实用工具，就会解压出来一个名为 Payload 文件夹。
2. 在 Payload 文件夹中找到当前 ipa 的 app 文件（基本就是和这个ipa名字一样的文件，app 后缀系统默认隐藏），右键显示包内容。
3. 进入到文件夹内，按照大小进行排序，你会发现所有的资源。


### 附: 查找 iOS 工程无用图片资源工具
[LSUnusedResources](https://github.com/tinymind/LSUnusedResources)

1. 点击 Browse，选择一个文件夹。
2. 点击 Search 开始搜索。
3. 等待片刻即可看到结果，可直接对搜索结果进行操作。

### 注意
针对减小 ipa 包体积的操作，我们必须考虑相关影响，以确保做出正确的决定。如果不做权衡的话，我们无法知道需要对程序做出什么样的改变。

