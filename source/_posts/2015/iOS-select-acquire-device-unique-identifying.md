---
title: iOS 如何选择和获取设备的唯一标识符
date: 2015-04-14 21:39:20
tags: 设备标识
categories: Objective-C
---
iOS 开发中常常需要将一些用户的使用日志等上传的服务器，方便反馈查询错误。要识别用户，就要选择一个标识符，通过这个标识符来识别这个用户的设备，这个标识符要能够保证一个设备上返回的值是一样的，并且在其他设备上不会出现相同的值，那么该如何获取这个到 `iOS 设备的标识符`。


### 概述
在 iOS 7 之前，曾经有过很多方法来识别用户的设备，从最原始的设备 UDID、mac 地址，到被各广告统计平台广泛使用的开源方案 OpenUDID 。

<!--more-->

但 iOS 7 之后，Apple 更加注重保护用户隐私，随着 AppStore 开始拒绝接受使用 UDID 的应用，到 mac 地址在在所有设备上都返回相同的值，再到 iOS7 上对剪贴板的限制，导致 OpenUDID 无法被不同应用共享相同的值，注定了上面提到的这些 id 们不得不退出历史的舞台。

那么我们该用什么来追踪和识别用户？

- `idfa`: 适用于对外：例如广告推广，换量等跨应用的用户追踪等。
- `idfv`: 适用于对内：例如分析用户在应用内的行为等。


### 被弃用的方法
#### UDID
`设备唯一标识符（Unique Device Identifier）`之前被各种国内外统计平台，应用开发商广泛使用，后 Apple 从 2013 年 05 月 01 日起拒绝接受使用 UDID 的应用。

#### Mac 地址
每一个网卡都有一个唯一的标识，即 `Mac 地址`，用来标识一个手机是绰绰有余，也有一些开源的方案也用到了它，国内 UMTrack 等也用它作为过主 id，随着 iOS 7 返回同样的值后，不得不被弃用。

#### OpenUDID
在 Apple 拒绝 UDID 后，`OpenUDID` 作为独立于 Apple 的开源方案，被广大的开发者所接受，各大统计广告平台都从 UDID 等方案切换到 OpenUDID 的方案，但同样由于 iOS 7 对剪贴板的限制，导致同一个设备上应用间，无法再共享一个 OpenUDID，即 OpenUDID 作为设备唯一标识的能力被大大削弱。

#### DeviceToken
`DeviceToken 是推送用的唯一标识`，但是用户如果没开推送，或者拒绝了推送，这个便没有了。


### 推荐的方法
#### idfa
- **全名**：advertisingIdentifier
- **示例代码**：
```objc
#import <AdSupport/AdSupport.h>
NSString *adIdStr = [[[ASIdentifierManager sharedManager] advertisingIdentifier] UUIDString];
```
- **适用于**：iOS 6.0 之后，适用于对外，例如广告推广，换量等跨应用的用户追踪等。
- **说明**：直译就是广告id， 在同一个设备上的所有 App 都会取到相同的值，是苹果专门给各广告提供商用来追踪用户而设的，用户可以在 设置|隐私|广告追踪 里重置此 id 的值，或限制此 id 的使用，故此 id有 可能会取不到值，但好在 Apple 默认是允许追踪的，而且一般用户都不知道有这么个设置，所以基本上用来监测推广效果，是戳戳有余了。
- **注意**：由于 idfa 会出现取不到的情况，故绝不可以作为业务分析的主 id，来识别用户。


#### idfv
- **全名**：identifierForVendor
- **示例代码**：
```objc
NSString *idfv = [[[UIDevice currentDevice] identifierForVendor] UUIDString];
```
- **适用于**：iOS 6.0 之后，适用于对内，例如分析用户在应用内的行为等。
- **说明**：顾名思义，是给 Vendor 标识用户用的，每个设备在所属同一个 Vender 的应用里，都有相同的值。其中的 Vender 是指应用提供商，但准确点说，是通过 BundleID 的 DNS 转的前两部分进行匹配，如果相同就是同一个 Vender，例如对于 com.somecompany.appone 和 com.somecompany.apptwo 这两个 BundleID 来说，就属于同一个 Vender，共享同一个 idfv 的值。和 idfa 不同的是，idfv 的值是一定能取到的，所以非常适合于作为内部用户行为分析的主 id，来标识用户，替代 OpenUDID。
- **注意**：如果用户将属于此 Vender 的所有 App 卸载，则 idfv 的值会被重置，即再重装此 Vender 的 App，idfv 的值和之前不同。


总之，Apple 越来越注重用户的隐私，也越来越去掌控一些关于隐私的设置，所以跟随 Apple 的政策，使用 `idfv` 和 `idfa` 作为设备的唯一标识符为最佳。


