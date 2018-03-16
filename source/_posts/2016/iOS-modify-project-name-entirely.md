---
title: iOS 使用 Xcode 彻底修改项目名（包含修改 CocoaPods 项目名）
date: 2016-03-24 21:01:22
tags:
categories: Objective-C
---
在 iOS 开发中，有时候两个项目有很多相似的地方，与其新建一个项目,不如直接在老项目的基础上修改，而修改项目的名字，会遇到很多麻烦，总是有很多文件、文件夹或者是项目设置项。而且都是不能随便改的，很多地方都会导致程序编译不了。下面的方法可以彻底修改项目的名字，你不会再看到任何有关于之前的项目名字。

### 做好备份
重命名项目时，记得先备份好一份。这里我将工程 TextDemo 重命名为 FormalDemo 。

<!--more-->

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/textDemo.png" width=600></img>
  
  
### 变更项目名称
打开 TextDemo.xcworkspace，如果没有使用 CocoaPods 就使用 TextDemo.xcodeproj 打开工程。直接将 TextDemo 命名为 FormalDemo 。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/projectName.png" width=450></img>
  
在弹出的窗口中选择 Rename 。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/rename.png" width=650></img>  
  
  
### 修改文件夹名称
将 TextDemo 和 TextDemoTests 分别修改为 FormalDemo 和 FormalDemoTests 。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/folder1.png" width=350></img> 
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/floder2.png" width=350></img> 
  
  
### 修改类 TextDemoTests.m
更改类名字时不能直接重命名，需要使用Xcode内置的更改类名功能。点击 @interface 后面的类名：

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/TextClass.png" width=550></img> 
  
Menu -> Edit -> Refactor -> Rename 。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/TextRename1.png" width=550></img> 
  
点击 Save 就可以了。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/TextRename2.png" width=750></img> 
  
  
### 全局搜索替换成新项目名字
此时目录名字都更换完成了，但是编译是不通过的，因为 Xcode 指向的还是之前的名字。接下来全局搜索旧的名字并替换成新的名字，这里只是替换一下注释。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/replaceAll.png" width=230></img> 
  
再次搜索旧的项目名，有些是需要我们手动修改的。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/modifyPlist.png" width=800></img> 
  
  
### 更改项目根目录下的文件夹名字并重新指定路径
打开项目文件夹，继续更改文件夹名字。  

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/FloderAgain.png" width=230></img> 
  
修改完后，发现工程的文件全部变红了。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/red.png" width=230></img> 
  
接下来，需要重新指定文件路径，选择文件夹 FormalDemo 。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/FormalDemoFolder.png" width=320></img> 
  
点击文件夹图标。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/Foldericon.png" width=340></img> 
  
在弹出的窗口中选择我们刚刚命名好的文件夹。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/FolderChoose.png" width=500></img> 
  
同样的方法重新指定 FormalDemoTests，然后发现红色的文件又都变回来了。


### 完成修改
然后重新编译，发现已经可以通过了，至此名字就完全修改过来了。修改后的目录结构：

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/Success1.png" width=260></img>
  
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/Success2.png" width=260></img> 
  
  
### CocoaPods 项目重命名
如果项目使用了 CocoaPods 管理第三方框架，重命名 CocoaPods 项目时。需要先将项目中的 CocoaPods 环境移除掉，然后按照上面的方法对工程进行重命名，然后再重新安装 CocoaPods 。

#### 移除相关 pod 文件
删除工程根目录文件夹下的 `Podfile、Podfile.lock` 及 `Pods 文件夹`，其中 Podfile 文件可以先备份一份，因为还要重新装回来。
  
#### 删除xcworkspace文件
删除工程根目录文件夹下 xxx.xcworkspace 文件。

#### 删除 Pods.xcconfig 及 libPods.a 的引用
使用 xxx.xcodeproj 文件打开工程，删除 Frameworks 组下的 Pods.xcconfig 及 libPods.a 文件的引用。

#### 删除 Build Phases 中的配置项
在工程设置中的 Build Phases 下删除 Check Pods Manifest.lock 、 Copy Pods Resources 和 Enable Pods Frameworks 选项。

#### 使用插件移除工程中的 CocoaPods 环境
手动卸载 CocoaPods 是件相对麻烦的事情，各种删除文件，又要打开项目设置各种配置信息，有时候还可能会报错。所有可以使用插件 cocoapods-deintegrate 来卸载 CocoaPods 。

1、首先打开终端在终端输入：
```objc
sudo gem install cocoapods-deintegrate
```

安装成功后会有以下提示：
```objc
Successfully installed cocoapods-deintegrate-0.2.1  
Parsing documentation for cocoapods-deintegrate-0.2.1  
1 gem installed
```

2、从终端进入到你的项目的根目录，然后执行：
```objc
pod deintegrate
```

成功后会有如下提示：
```objc
Deintegrating Test.xcodeproj  
Deintegrating target Test  
Deleted 1 'Copy Pods Resources' build phases.  
Deleted 1 'Check Pods Manifest.lock' build phases.  
Deleted 1 'Embed Pods Frameworks' build phases.  
Removing Pod libraries from build phase:  
- libPods.a
Deleting Pod file references from project  
- Pods.debug.xcconfig
- Pods.release.xcconfig
- libPods.a
Deleted 1 empty `Pods` groups from project.  
Deleted 1 empty `Frameworks` groups from project.  
Removing `Pods` directory.
Project has been deintegrated. No traces of CocoaPods left in project.  
Note: The workspace referencing the Pods project still remains.
```

3、至此，CocoaPods 已经完全从你的项目中移除了，但 CocoaPods 的文件还是存在的，手动删除更目录下的 Podfile、Podfile.lock 和 .xcworkspace 文件即可。



### 补充点
#### 修改 PCH 文件和其路径
如果工程中有 PCH 文件还需要重命名 PCH 文件，并重新指定其路径。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/PrefixFile.png" width=700></img>
  
#### 修改 Library 和 Framework 搜索路径
改名后编译报 warning: directory not found for option 这样的错误（原因是找不到链接的库或者文件）。
选择工程 targets -> Build Settings 将其中的 Framework Search Paths 和 Library Search Paths 这两项重新指定正确的路径即可。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/SearchPath.png" width=700></img>

#### 修改 Scheme 名称
点击要编辑的 Scheme，然后选择 Edit Scheme…

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/EditScheme.png" width=320></img>

在弹出的窗口中选择 Manage Schemes… ，然后直接回车编辑即可。

<img src="http://omkug1guu.bkt.clouddn.com/iOS-modify-project-name-entirely/RenameScheme.png" width=660></img>

待续…



