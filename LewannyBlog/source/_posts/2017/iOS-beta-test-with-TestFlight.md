---
title: iOS 开发使用 TestFlight 进行 Beta 测试
date: 2017-03-20 20:37:59
tags: TestFlight
categories: Objective-C
---

应用开发过程中 `Beta` 测试是必须的。`TestFlight` 就是用于 Beta 版测试的。不同与较老的 adhoc 测试包方法，TestFlight 不需要测试人员的设备 UDID，也没有 100 台设备的上限。目前 TestFlight 已经被苹果收购，需要 iOS8 及以上版本的 iOS 设备才能运行。TestFlight 整合进了 iTunes Connect，开发者可以通过电子邮件来邀请测试员来进行测试，这样开发者就可以更简单地让你的同行非同行、你的上级、你的客户、你所想要一起参与测试的人一起来测试应用。

### 什么是 TestFlight
TestFlight 的测试方式分为内部测试和外部测试两种，从功能上讲，这两种方式都可以为测试人员在测试阶段对你的 App 进行测试。

<!--more-->

#### 内部测试
所谓的内部主要是指 iTunes Connect 上 在你的开发团队里参与技术开发或者管理的成员。最多可以邀请 25 个内部成员。无审核限制，也就是说只要你把你的 App 上传到 iTunes Connect 上之后内部成员就可以开始进行内部测试了，无需审核。在邀请发出后，有效测试时间为 90 天。

#### 外部测试
而与之相对的外部测试，指的是那些不属于你的团队或者公司的测试者，外部测试人员的上限是 2000 人，但在你邀请外部成员参与测试之前，需要先通过苹果的审核，一般审核会在一天左右。而这一限制在内部成员上就没有。在邀请发出后，有效测试时间为 60 天。



### TestFlight 的优势
1. 老旧的 Adhoc 测试包方法需要供参与 app 测试人员的设备 UDID，并且开发者需要将这些设备的 UDID 添加到开发者中心，每次有新的测试人员加入，需要重新生成 profiles，重新打包，而且只能添加100台设备的限制，TestFlight 这方面限制较小。

2. TestFlight 测试只需要参与 app 测试人员提供一个邮箱，开发者登录 iTunes Connect，给测试者发送邀请，测试者接受邀请，然后通过 TestFlight 应用，下载安装程序。不再次需要用户提供设备的 UDID，最重要的一点是没有了 100 台设备的限制，一般内部测试人员最多 25 个人，外部测试人员最多 2000 个人，也不需要每次都重新配置证书，不需要每次都手动发送 ipa 包, 直接上传到 iTunes 即可。

3. 当然也可使用蒲公英等其他方法参与测试。


### 如何使用 TestFlight
#### 创建应用

1. 进入 [iTunes Connect](https://itunesconnect.apple.com/) 网址：iTunes Connect ，输入账号密码登录至 iTunes Connect。

2. 登陆成功后点击 MyApp 进入到应用列表。

  <img src="https://www.lewanny.com/images/iOS-beta-text-with-TestFlight/myApp.png" width=520>
  
3. 如果是初次创建的 App，需要创建一个 iTunes Connect Record。如果你的 App 已经创建好了，可以直接跳过这段，如果你要新建一个 App 的话，可以按照下面的步骤来做。


#### 如何创建应用

1. 登录 [iTunes Connect](https://itunesconnect.apple.com/login) 。在 iTunes Connect 的首页，点击 My Apps 图标。

2. 点击 + 号按钮，创建一个新的应用。

  <img src="https://www.lewanny.com/images/iOS-beta-text-with-TestFlight/addNew.png" width=230>
  
3. 填写好如下弹窗内的信息，点击创建按钮完成创建即可，刚刚创建好的应用便会显示在 My App 列表中。

  <img src="https://www.lewanny.com/images/iOS-beta-text-with-TestFlight/info.png" width=450>
  
4. 提交 App 到 `iTunes Connect`。在 Xcode 中配置好版本和证书信息。
选中 `菜单 -> Product ->Archive` 将当前代码归档。
归档完成后选择 `Organizer -> Archives -> Validate` 按钮来检测该归档文件是否符合要求。
验证通过之后，你可以选择 `Submit` 来提交这个归档至 iTunes Connect。
注意：提交的包必须是 `App Store` 包。

5. 每当你提交一个新的 build 之后，在 iTunes Connect 的列表中都会对应的增加一个 `build`。

  <img src="https://www.lewanny.com/images/iOS-beta-text-with-TestFlight/build.png" width=350>
  

#### 邀请 TestFlight 测试人员
1. 选择 TestFlight，在 TestFlight 中填写基本信息接收反馈的邮箱地址，隐私政策网址等，然后构建好需要测试的版本信息。

  <img src="https://www.lewanny.com/images/iOS-beta-text-with-TestFlight/chooseTest.png" width= 720>
  
2. 按照提示才操作来邀请你的测试人员（分内部和外部，其中 iTunes Connect 为内部）。

  <img src="https://www.lewanny.com/images/iOS-beta-text-with-TestFlight/visit.png" width=720>
  
3. 填写好对应测试人员的邮箱，发送好对应邮件即可。测试人员需在 App Store 中下载 TestFlight，之后会收到两封邮件，一封是邀请你加入测试组的邮件，点击同意即可，另一封是成功加入测试组的通知，点击 start testing 然后跳转到有加粗黑色字体的8个字母测试码的页面，然后点击 Redeem，输入这8个字母即可下载测试app。（现在会直接跳转。）

4. 开发者发布新的版本测试人员可直接在 TestFlight 查看对应版本即可。


另外 TestFlight 还有很多其他的功能和用途，还是挺不错的，这里不再一一记录了。具体可登陆 [iTunes Connect](https://itunesconnect.apple.com/) 查看。