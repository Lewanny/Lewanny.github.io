---
title: iOS 开发 OC 拓展类的几种方式
date: 2014-10-03 19:56:49
tags:
categories: Objective-C
---

#### 继承
继承：子类 `subClass`
作用：可以通过类的继承来增添父类的属性和方法。
写法：在 .h 文件中
```objc
@interface Student : Person
```
<!--more-->

#### 类目
类目：`Category`，也叫分类、类别。
作用：可以增添父类的方法，但是不能定义变量。多用于不知道源码的情况下，对父类进行拓展。例如用来拓展系统类。
写法：在 .h 和 .m 中，文件名为 “父类名+分类名” 格式。在 `@interface` 和 `@implementation` 后 “父类名(分类名)” 。
```objc
// NSString+autoResize
@interface NSString (autoResize)
@implementation NSString (autoResize)
```

#### 延展
延展：`Extension`
作用：定义私有方法，可以隐藏不对外公布的方法，多用于隐藏一些中间步骤的方法。
写法：在 .m 文件中的 `@implementation` 前实现。
```objc
@interface Person() {    
  NSString * _age;
}
  - (void)logAge;
@end
```

#### 协议
协议方法：`protocol`
作用：可以实现类似多继承的方法，一个类遵守多个协议。
写法：协议只有 .h 文件，定义了方法。
```objc
@required //默认是必须实现的
  - (void)method1;
  - (void)method2;
                       
@optional //可以选择实现
  - (void)method3;
```
