---
title: iOS 判断系统版本号的多种方法
date: 2015-10-04 21:09:11
tags: 版本号
categories: Objective-C
---

iOS 开发中，有一些启用掉的 API 运行在高版本的系统上可能会无效等问题，在低版本系统上使用新的 API 也是如此，甚至会引起程序的 Crash 。所以此时就需要判断用户设备的版本，根据不同的版本号使用对应的方法来适配不同的系统。

例如以下 NSCalendar 的方法：
```objc
// iOS 8 之前是使用这个方法 currentCalendar 来获取 NSCalendar 对象，如果在 iOS 8 或 iOS 9 上使用就可能会有问题。
NSCalendar *calendar = [NSCalendar currentCalendar]; 
// iOS 8 以后，可以使用下面的方法获取 NSCalendar 对象。
NSCalendar *calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
```
<!--more-->

#### 比较系统版本号
获取当前`系统版本`：
```objc
NSString *version = [UIDevice currentDevice].systemVersion;
```

将版本号 String 转换成 Double 进行比较：
```objc
NSCalendar *calendar = nil;
if (version.doubleValue >= 8.0) { 
    // iOS 系统版本 >= 8.0
   calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
} else{ 
    // iOS 系统版本 > 8.0
   calendar = [NSCalendar currentCalendar]; 
}
```


#### 比较 Foundation 框架的版本号
iOS 系统版本号提升的时候，`Foundation 的版本`也会同时提高。
```objc
#if TARGET_OS_IPHONE
#define NSFoundationVersionNumber_iPhoneOS_2_0	678.24
#define NSFoundationVersionNumber_iPhoneOS_2_1  678.26
#define NSFoundationVersionNumber_iPhoneOS_2_2  678.29
#define NSFoundationVersionNumber_iPhoneOS_3_0  678.47
#define NSFoundationVersionNumber_iPhoneOS_3_1  678.51
#define NSFoundationVersionNumber_iPhoneOS_3_2  678.60
#define NSFoundationVersionNumber_iOS_4_0  751.32
#define NSFoundationVersionNumber_iOS_4_1  751.37
#define NSFoundationVersionNumber_iOS_4_2  751.49
#define NSFoundationVersionNumber_iOS_4_3  751.49
#define NSFoundationVersionNumber_iOS_5_0  881.00
#define NSFoundationVersionNumber_iOS_5_1  890.10
#define NSFoundationVersionNumber_iOS_6_0  992.00
#define NSFoundationVersionNumber_iOS_6_1  993.00
#define NSFoundationVersionNumber_iOS_7_0 1047.20
#define NSFoundationVersionNumber_iOS_7_1 1047.25
#define NSFoundationVersionNumber_iOS_8_0 1140.11
#define NSFoundationVersionNumber_iOS_8_1 1141.1
#define NSFoundationVersionNumber_iOS_8_2 1142.14
#define NSFoundationVersionNumber_iOS_8_3 1144.17
#define NSFoundationVersionNumber_iOS_8_4 1144.17
#define NSFoundationVersionNumber_iOS_8_x_Max 1199
#define NSFoundationVersionNumber_iOS_9_0 1240.1
#endif
```
```objc
if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0) { 
    // iOS 系统版本 >= 8.0
    calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
} else {
    calendar = [NSCalendar currentCalendar];  
}
```


#### 通过特定的类来判断
例如 UIAlertController 是 iOS 8 以后才有的类：`NS_CLASS_AVAILABLE_IOS(8_0)`
可以通过判断 `NSClassFromString(@"UIAlertController")` 是否为 NULL 对象，从而来判断当前系统版本是否大于 8.0 。
```objc
NSLog(@"%@,%@",NSClassFromString(@"UIAlertController"), NSClassFromString(@"CustomClass"));
打印结果：UIAlertController---UIAlertController, CustomClass---(null)
```

```objc
// iOS 系统版本 >= 8.0
if (NSClassFromString(@"UIAlertController")) { 
  // do something
}
```


#### 通过特定方法进行判断
`NS_AVAILABLE(10_10, 8_0)`：意为此方法是 iOS 8 之后才出现的，例如下面 NSString 的方法：
```objc
- (BOOL)containsString:(NSString *)str NS_AVAILABLE(10_10, 8_0);
```

```objc
if ([@"" respondsToSelector:@selector(containsString:)]) { 
    // iOS系统版本 >= 8.0
}
```
