---
title: CocoaPods 的安装使用和常见问题
date: 2016-01-27 13:01:06
tags: CocoaPods
categories: Objective-C
---
CocoaPods 是一个负责管理 iOS 项目中第三方开源库的工具，iOS 开发不可避免地要使用第三方开源库，CocoaPods 的出现使得我们可以节省设置和更新第三方开源库的时间，下面将介绍如何安装使用 CocoaPods 以及一些常见问题的解决方案。

### CocoaPods 安装步骤
#### Ruby 环境搭建
CocoaPods 是用 Ruby 写的，使用 CocoaPods 需要 `Ruby 环境`，Mac OS 本身自带 Ruby，但最好还是更新一下，因为升级了系统之后，可能会出现一些意想不到的情况，首先打开终端 Terminal 。

<!--more-->

1. 查看下当前ruby版本： ruby -v 。
	<img src="http://omkug1guu.bkt.clouddn.com/iOS-CocoaPods-usage-and-problem/RubyVersion.png" width = 900></img>
	
2. 升级ruby： `sudo gem update —system` 。

3. 由于国内防火墙限制我们，可以用淘宝的Ruby镜像来访问CocoaPods。终端输入如下命令：
```objc
gem sources –remove https://rubygems.org/ 。
gem sources -a https://ruby.taobao.org/ 。
gem sources -l （用来检查使用替换镜像位置成功）。
```


#### 下载安装 CocoaPods
1. 命令： `sudo gem install cocoapods`，Mac OS 10.11 以上系统使用命令： `sudo gem install -n /usr/local/bin cocoapods`。
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-CocoaPods-usage-and-problem/CocoaPodsInstall.png" width = 900></img>

2. 使用搜索命令 `pod search AFNetworking` 查找某一个库，看 CocoaPods 有没有安装好，如下便安装成功。
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-CocoaPods-usage-and-problem/%E5%AE%89%E8%A3%85%E6%88%90%E5%8A%9F.png" width = 900></img>
  
  

### CocoaPods的使用
#### 进入到工程根目录
打开项目所在的文件夹，在终端敲入 `cd Desktop/CocoaPodsText` 或将文件夹拖拽到终端，进入到项目所在文件夹（根目录）。

#### 建立 Podfile (配置文件)

- 终端输入： vim Podfile 。
- 接着输入： i 进入 insert 模式。
- 内容按这个格式输入：
```objc
target ‘YourProjectName’ do
  pod ‘MBProgressHUD’
　pod ‘ASIHTTPRequest’, ‘~> 1.8.2’
　pod ‘SDWebImage’, ‘~> 3.7.3’
end 　 
```
> 如果你不知道这些框架目前的版本是，可以使用命令 pod search + 框架名称 查看相应的信息，不写版本默认为框架的最新版本。

- 然后按 Esc ，并且输入 shift + ：号进入 vim 命令模式，然后在冒号后边输入 wq ，按回车键，保存并且退出，完成后会发现对应项目根目录中多一个 Podfile 文件。
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-CocoaPods-usage-and-problem/Podfile.png" width = 750></img>
  
> Podfile 文件的创建编辑可以使用终端操作，也可直接创建文件打开文件编辑。


#### 安装第三方类库
终端 cd 到项目根目录小，然后输入 `pod install`，等待一会，框架就自动安装好了，如图所示，多出了4个文件夹/文件夹。
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-CocoaPods-usage-and-problem/%E5%AE%89%E8%A3%85%E5%BA%93%E6%88%90%E5%8A%9F.png" width = 780></img>
> 注意：现在打开项目不是点击 podTest.xodeproj 了，而是点击 `podsTest.xcworkspace` (白的图标)。


#### 常用命令
1. `pod install –no-repo-update –verbose` 关掉索引更新并安装且体现下载过程。
2. `pod install –no-repo-update —verbose` 同为需要添加删除时。
3. `pod update –no-repo-update —verbose` 更新 pod (更新第三方库时)。
4. `pod —-version` 查看当前 pod 版本。
5. `sudo gem uninstall cocoapods` （卸载当前的CocoaPods）。
6. `sudo gem install cocoapods -v 0.38.1` （安装 0.38.1 版本的 CocoaPods ）。



### 关于 CocoaPods 的一些问题总结
#### 出现导入头文件问题，未找到 xxx.h
1. Xcode 选择 Target -> Build Settings 菜单，找到 `User Header Search Paths` 设置项。
2. 新增一个值 `${SRCROOT}`，并且选择为 `Recursive`。

#### 图形化插件
1. 终端中执行： curl -fsSL https://raw.github.com/supermarin/Alcatraz/master/Scripts/install.sh | sh 。
2. 安装完成重启 Xcode 即可， 在 Xcode -> window -> package manger 可以找到 CocoaPods 选项，使用起来简单方便。（Xcode8 导致所有插件失效）

#### 如何编译从 github 上 checkout 下来的一个已包含 CocoPods 类库的项目
1. 删除工程文件夹下的 Podfile、Podfile.lock 及 Pods 文件夹。
2. 删除 xcworkspace 文件。
3. 使用 xcodeproj 文件打开工程，删除 Frameworks 组下的 Pods.xcconfig 及 libPods.a 引用。
4. 在工程设置中的 Build Phases 下删除 Check Pods Manifest.lock 及 Copy Pods Resources 。

#### 安装 CocoaPods 时出现错误
	ERROR:  While executing gem ... (Errno::EPERM) Operation not permitted - /usr/bin/pod
执行 `sudo gem install -n /usr/local/bin cocoapods` 即可解决。

#### Mac OS 升级到 10.11 后当 pod 命令失效
报错：`-bash: pod: command not found`。
这是你之前有 CocoaPods，但是输入比如 pod update 或者 pod install 之后命令失效，输入：
`sudo gem install -n /usr/local/bin cocoapods`
然后在执行命令，就会解决。

#### 执行 pod update 和 pod install 速度比较慢的问题
在国内，你有可能就被墙了，所以就不要用 pod update 和 pod install 了，使用：
`pod update –verbose –no-repo-update` 和 `pod install –verbose –no-repo-update` 来代替。

#### 报错：[!] Unable to add a source with url https//: github.com/CocoaPods/Spece.git named master

   <img src="http://omkug1guu.bkt.clouddn.com/iOS-CocoaPods-usage-and-problem/Unable.png" width = 780></img>

如果你是初次初次使用CocoaPods， 可能会出现 `Setting up CocoaPods master repo` 的提示， 说明 CocoaPods 在将它的信息下载到你的电脑 `~/.cocoapods` 里，使用 cd ~/.cocoapods 到该目录里，用 `du -sh *` 命令来查看文件大小，每隔几分钟查看一次，我的这个目录最终大小是 123M ，就是完成了。

上面的错误就是因为你本地 repo 的问题，你可以尝试使用 pod update 更新，或者可以到网上下载 master 放到 ~/.cocoapods 里面即可。

> 打开本地目录 ~/.cocoapods，可以看到里面存放的是所有库的podspec 文件，这里面包含了所有可以使用 CocoaPods 管理库的版本和地址等信息，关于 podspec 文件可参考我的另一篇文章 ”使用CocoaPods 制作和关联使用私有库“


#### 报错： [!] The dependency XXX is not used in any concrete target.
需要在 Podfile 文件中指定 target 工程，未指定便会报这个错。
```objc
target 'FMGApp' do
  pod 'JazzHands'
  pod 'SSKeychain', '1.2.3'
  pod 'UMengSocial', '4.2.3'
end
```
持续更新中…


