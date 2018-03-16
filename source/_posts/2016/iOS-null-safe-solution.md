---
title: iOS null 导致程序 Crash 的解决方案
date: 2016-11-25 21:25:31
tags: NullSafe
categories: Objective-C
---

### 现象
iOS 开发中经常需要与服务器进行数据交互，JSON 是常用的高效简洁的数据格式。然而当服务器返回的数据中有些字段为空：`"somevalue": null` 这样的数据解析成对象之后，如果直接向这个对象发送消息就会直接造成程序 Crash。


### 错误类型
向一个空对象发送消息后造成 Crash 的错误日志为:
```objc
-[NSNull length]: unrecognized selector sent to instance 0x388a4a70
```

<!--more-->

### 解决方案
#### 对可能出现空的字段进行非空判断
会造成 Crash 的字段解析成的对象是 `NSNull` 类型的，所以可以直接判断是不是此类型，这样的字段会比较多所以也会比较繁琐，也容易遗漏，但是也比较保险。
```objc
if (![isKindOfClass:[NSNull class]]){.....}
```

#### 字符串匹配, 替换 null 为 为空字符 “”
在获取到服务器返回的 JSON 时，返回结果时 string 对象，于是就先替换 null 为 为空字符””，然后再解析可避免 Crash 问题，这种方法比较巧妙，但若服务器的数据不太整洁也会有一定的问题。
```objc
json = [jsonStr  stringByReplacingOccurrencesOfString:@":null" withString:@":\"\""];
```

#### 解析数据时把 NSNull 类型的值替换成 nil
使用宏定义。
```objc
#define VerifyValue(value)\
({id tmp;\
if ([value isKindOfClass:[NSNull class]])\
tmp = nil;\
else\
tmp = value;\
tmp;\
})\
```

在解析数据时，把接收到的 NSNull 类型的值替换成 nil 。
```objc
contact.contactPhone = VerifyValue(contactDic[@"send_ContactPhone"]);
```

#### 使用 AFNetworking 提供的方法
如果是使用 AFNetworking 这个库做网络请求的话，可以用以下代码，自动帮你去掉空值
```objc
self.removesKeysWithNullValues = YES;
```

#### 第三方库 NullSafe
[Nullsafe](https://github.com/nicklockwood/NullSafe) 是一个 Category ，在运行时操作，把这个讨厌的空值置为 nil ，而 nil 是安全的，可以向 nil 对象发送任何 message 而不会 Crash ，这个 category 使用起来非常方便，只要加入到了工程中就可以了。



### 附： NullSafe 的工作原理
当我们给一个 NSNull 对象发送消息的话，可能会崩溃（ null 是有内存的），而发送给 nil 的话，是不会崩溃的。把发送给 NSNull 的而 NSNull 又无法处理的消息经过如下几步处理:

1. 创建一个方法缓存，这个缓存会缓存项目中类的所有类名。
2. 遍历缓存，寻找是否已经有可以执行此方法的类。
3. 如果有的话，返回这个 NSMethodSignature 。
4. 如果没有的话，返回 nil , 接下来会走 forwardInvocation:方法。
5. [invocation invokeWithTarget:nil]; 将消息转发给 nil。

在 OC 中，系统如果对某个实例发送消息之后，它（及其父类）无法处理（比如，没有这个方法等），系统就会发送 methodSignatureForSelector 消息，如果这个方法返回非空，那么就去执行返回的方法，如果为 nil, 则发送 forwardInvocation 消息。这样就完成整个转发链了。

