---
title: iOS 使用 RVM 升级 Ruby 解决 CocoaPods 无法生成 xcworkspace 文件的问题
date: 2017-01-04 13:38:21
tags: CocoaPods
categories: Objective-C
---

在安装 CococaPods 时，出现执行 pod install 后成功后却没有生成对应的 xcworkspace 文件问题，查找后发现是本地 Ruby 环境版本过低，需要升级至 2.2.2 以上。在终端中输入 `ruby -v` 查看 Ruby 版本，果然版本为 2.0.0 。

升级 Ruby 环境可以使用 `RVM` ，也就是 Ruby Version Manager , Ruby 版本管理器。RVM 包含了 Ruby 的版本管理和 Gem 库管理 (gemset)。RVM 可以让你拥有多个版本的 Ruby，并且可以在多个版本之间自由切换。

下面的命令全部在终端中进行。

<!--more-->


### RVM 安装
```objc
curl -L get.rvm.io | bash -s stable
```
等待一段时间后就可以成功安装好 RVM 。


### 测试是否安装正常
```objc
rvm -v
```
出现版本号即安装成功。


### 列出已知的 Ruby 版本
```objc
rvm list known
```


### 安装指定版本的 Ruby
```objc
rvm install 2.3.3
```
RVM 可以让你拥有多个版本的 Ruby，如果安装不成功，可以试着安装其他的版本，高于 2.2.2 即可。


### 查看当前 Ruby 版本和列表
使用 `ruby -v` 查看当前的 Ruby 版本 。
使用 `rvm list` 查看当前所有的 Ruby 版本列表 。


### 设置默认的 Ruby 版本
```objc
rvm default 2.3.3
```
到此，Ruby 环境变更新完，重新到你的工程目录下执行 pod install 便可正确安装 CococaPods 。


### 更新可能遇到的问题
更新过程中报错如下：
```objc
Error running 'Requirements_oxs_brew_update_system ruby -2.3.3',
showing last 15 lines of /User/Lihongyuan/.rvm/log/848784602_ruby-2.3.3/update system.log
Requirements installation failed with status: 1.
```

解决方法：卸载 `home-brew` 。
```objc
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

然后再次安装即可。
```objc
rvm install 2.3.3
```


