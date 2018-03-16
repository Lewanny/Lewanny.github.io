---
title: iOS 私有 API、未公开 API 和公开 API 的对比和使用
date: 2016-12-22 23:07:07
tags: 私有 Api
categories: Objective-C
---

iOS 的 API 除了公开的 API 以外，还有两类，分别是私有 API 和未公开 API 。iPhone 编程中一些非常精致强大的 API 都包含在私有 API 或未公开的 API 中，但未公开的 API 非常不稳定，变更的可能性很大。同时，最大的问题是使用了私有 API 的程序是不能上架 App Store 的。下面是三种 API 的介绍和对比。

--- 

### 公开API（Published API）
#### 介绍
`Published API`（公开的 API）：又称 `Documented API`（文档中记录的 API）。也就是是苹果通过 Cocoa Touch 向全世界第三方开发者公开的所有 API 。

<!--more-->

#### 使用
所有的公有 API 的介绍和使用方法都有记录在 `Apple 的官方文档`中。


### 未公开API（UnPublished API）
#### 介绍
`UnPublished API`（未公开 API）：又称 `Undocumented API`（文档中未记录的 API ）。是指虽然放在 Frameworks 框架中，但是却没有在苹果的官方文档中有使用说明、代码介绍等记录的 API 。
按苹果的说法，未公开的 API 是还`不够成熟，可能还会变动的 API` ，等完全成型了后会变成公开的 API ，但是目前不对其提供承诺，就是系统版本升级后可能会失效。

#### 使用
是目前不对其提供未公开 API 虽然未公开，但已经放在了公开的 Frameworks 框架中，所以他的使用方法和公开 API 一样，只要知道了对应的方法名，直接调用即可。

#### 补充
如果应用中使用了未公开的 API ，程序是不能在苹果的 App Store 上发布的。当然也可以通过某些加密的方法躲过审核。


### 私有API（Private API）
#### 介绍：
`Private API`（私有API）：指的是 SDK 下的 `PrivateFrameWorks` 框架下的 API ，真实存在于 Cocoa Touch 中，私有 API 是苹果明确规定不能使用的 API 。

一旦你的程序中有私有 API，在上传 AppStore 的时候也是会被拒绝的。如果当作小范围软件的话就可以尝试一下，比如再 Cydia 上发布，91 上发布，条件不会很苛刻。


#### 私有 Frameworks 的位置
私有的 Frameworks 存放在 Xcode 包的目录下，这里以 `ChatKit` 举例：
	
	Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS9.2.sdk/System/Library/PrivateFrameworks/ChatKit.framework
	
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-private-API-acquire-and-usage/ChatKitPosition.png" width=750>
  

#### 如何获取私有 API
使用 class-dump 获取对应 Frameworks 的头文件。

##### 下载class-dump
可以在 [class-dump](http://stevenygard.com/projects/class-dump/) 官网 直接下载，或者下载 [class-dump 源代码](https://github.com/nygard/class-dump) 编译得到。
	
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-private-API-acquire-and-usage/DumpFile.png" width=180>

##### class-dump 放到本地目录
把 class-dump 放到本地的 `/usr/local/bin` 目录下（有的系统是 “/usr/bin” ），在终端输入 class-dump ，显示 class-dump 后就可以正常使用了。如下图：
	
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-private-API-acquire-and-usage/DumpVersion.png" width=600>
  
##### 修改 class-dump 权限
进入到 class-dump 所在目录，执行如下命令，修改 class-dump 的权限为可读可写。
```objc
chmod 777 class-dump
```

##### 执行 class-dump -H 命令
执行如下命令便可导出对应私有 Frameworks 的头文件，我以 ChatKit.framework 为例。
```objc
class-dump -H /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneOS9.2.sdk/System/Library/PrivateFrameworks/ChatKit.framework -o/Users/apple/Desktop/Headers
```

##### 解释
`class-dump` -H class-dump 导出头文件命令。
`/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneOS9.2.sdk/System/Library/PrivateFrameworks/ChatKit.framework` 需要 dump 文件的路径。
`-o/Users/apple/Desktop/Headers` dump 出来头文件的文件夹路径。


导出的头文件：

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-private-API-acquire-and-usage/DumpChartKit.png" width=470>
  
 
#### 如何使用私有 API
现在我们有了对应的 Frameworks 文件和头文件，然后新建一个名为 Headers 的文件夹 将 dump 出来的头文件放到 Headers 文件夹中，在把 Headers 放到 Framework 文件中。然后在工程中导入 Frameworks ，就可以以静态库的方式在我们的代码中使用这些私有 API 函数了。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-private-API-acquire-and-usage/Success.png" width=650>
  
  
#### 补充
现在 iOS 9.3 以后，苹果取消了所有私有 API 的开放，所以现在不能拿到私有的 API 了，只能拿到虚拟机的私有 API，方法同上。目录如下：

	/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator9.2.sdk/System/Library/PrivateFrameworks/xx.framework

	
	