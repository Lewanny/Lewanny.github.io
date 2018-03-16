---
title: iOS 打 ipa 包的几种实用方法
date: 2016-08-21 19:21:43
tags: 
categories: Objective-C
---
iOS 开发中无论是提交你的应用到 App Store，打 Ad Hoc 测试包，还是制作企业版二维码下载，都需要导出 ipa 包。下面介绍几种打 ipa 包的实用方法。

### 使用 Xcode 的 Archive 功能（ Apple 推荐的方式）
选择 `Xcode 菜单 —> Archive —> 完成后 Export —> Ad Hoc` 然后选择你的开发者账号和保存位置导出即可，如下：

<!--more-->

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/Achive.jpeg" width=700></img>
  
局限性：一般多人开发时都是采用 provisioning profile + P12 文件来进行真机调试，上述方法在最后导出 ipa 包时需要输入 AppleID，到这一步采用 provisioning profile + P12 真机调试的方式由于账号权限 Xcode 配置等原因可能会导致相关问题不能导出。如以下等问题：

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/error1.jpeg" width=550></img>
  
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/error2.jpeg" width=550></img>

除了这种方式外，还有其他比较实用的四种方法，接下来主要介绍这几种方法。


### 通过 iTunes 导出(推荐)
这种方式是在 Xcode 编译产生出 .app 包的基础上进行进一步处理，通过简单的压缩以及改后缀名即可实现 ipa 打包。
直接打印 [NSBundle mainBundle] 目录，然后打开次目录，会看到将目录下有应用的 .app 文件。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/iTunes1.jpeg" width=630></img>

将得到的 .app 文件直接拖动到 iTunes 中，就会在 itunes 中自动生成 ipa, 然后选中生成的 ipa，再直接拖动到桌面，在桌面就生成 ipa 文件了。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/iTunes2.jpeg" width=630></img>
  
  
### 修改后缀名为 .ipa
道理同方法一。

1. 新建名为了 “Payload” 的文件夹，注意名字要一字不差。
2. 将你的 .app 包放到P ayload 文件夹中，注意 app 的名字不做任何更改，就用 Xcode 生成的 app 名称。
3. 在 Payload 文件夹上右键压缩成 zip，然后将生成的 .zip 文件后缀改成 .ipa 即可。


### 命令行实现打包
1. 打开终端，到需要打包的工程目录下，即 .xcodeproj 工程文件目录下。
2. 执行 `xcodebuild clean` 命令。

    <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/Command1.jpeg" width=630></img>
    
3. 然后执行 `xcodebuild -target ** -configuration Release`，其中 ** 为你的应用名，这个过程可能需要等待一会。

    <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/Command2.jpeg" width=630></img>
    
4. 完成后在终端输入 `xcrun -sdk iphoneos PackageApplication -v ** -o ##`，然后回车，即可在指定的目录下得到 .ipa 文件：	

	- `-v` 后面的 ““ 为上一条命令执行后得到的路径，也就是 build 后 .app 文件的路径，如图7，我的为：
		`/Users/lihongyuan/Desktop/LangQin/build/Release-iphoneos/.app`
	- `-o` 后面的 “##” 为到出 ipa 文件的完成路径，可自定义。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/Command3.jpeg" width=630></img>
    
5. 第三步过后会发现工程目录下多出一个build文件夹，然后 build -> Release -> iphoneos 找到下面的 .app 文件：
 
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/Command4.jpeg" width=550></img>
    
6. 终端输入 `xcrun -sdk iphoneos PackageApplication -v + 空格`，然后把 .app 文件直接拖进终端，因为现在已经在当前路径下了。
在终端继续输入 `-o + 空格`，然后把 .app 文件直接拖进终端，然后把 .app 改为 .ipa，回车，在 .app 文件当前的路径下，发现生成了 .ipa 文件:

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-achive-ipa-package-methods/Command5.jpeg" width=420></img>



### 脚本实现
编写一个全自动编译脚本，从而不用打开 Xcode 编译运行即可实现打包，这种方法也十分快捷，一劳永逸，这里不具体展开。
缺点：不出错还好，一旦有语法错误或者其他错误出现就不好处理。



