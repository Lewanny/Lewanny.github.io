---
title: 应用 Crash 捕获库 NullSafe 和 AvoidCrash 对比
date: 2016-11-28 22:28:06
tags: NullSafe
categories: Objective-C
---
在前 “iOS null 导致程序 Crash 的解决方案” 文章中提到使用 NullSafe 框架在运行时操作，把空值置为 nil ，从而防止操作 nil 值引起程序 Crash 。针对防止程序 Crash 的框架还有 AvoidCrash ，下面对两个框架进行了对比。

[Nullsafe](https://github.com/nicklockwood/NullSafe)
[AvoidCrash](https://github.com/chenfanfang/AvoidCrash)


### 不同的作用
#### NullSafe
NullSafe 可在运行时，把虽有的空值置为 nil ，可有效的防止所有的空值问题引起的Crash 。

<!--more-->

#### AvoidCrash
AvoidCrash 利用 runtime 技术对 Foundation 一些常用并且容易导致崩溃的方法进行处理。

目前可以防止崩溃的方法有:
```objc
1. NSArray
NSArray的快速创建方式 NSArray *array = @[@”chenfanfang”, @”AvoidCrash”];
+ (instancetype)arrayWithObjects:(const id _Nonnull __unsafe_unretained *)objects count:(NSUInteger)cnt
- (id)objectAtIndex:(NSUInteger)index


2. NSMutableArray
- (id)objectAtIndex:(NSUInteger)index
- (void)setObject:(id)obj atIndexedSubscript:(NSUInteger)idx
- (void)removeObjectAtIndex:(NSUInteger)index
- (void)insertObject:(id)anObject atIndex:(NSUInteger)index


3. NSDictionary
NSDictionary 的快速创建方式 NSDictionary *dict = @{@”frameWork” : @”AvoidCrash”}; // 这种创建方式其实调用的是2中的方法。
- (instancetype)dictionaryWithObjects:(const id _Nonnull unsafe_unretained *)objects forKeys:(const id _Nonnull unsafe_unretained *)keys count:(NSUInteger)cnt


4. NSMutableDictionary
- (void)setObject:(id)anObject forKey:(id)aKey
- (void)removeObjectForKey:(id)aKey


5. NSString
- (unichar)characterAtIndex:(NSUInteger)index
- (NSString *)substringFromIndex:(NSUInteger)from
- (NSString *)substringToIndex:(NSUInteger)to {
- (NSString *)substringWithRange:(NSRange)range {
- (NSString )stringByReplacingOccurrencesOfString:(NSString )target withString:(NSString *)replacement
- (NSString )stringByReplacingOccurrencesOfString:(NSString )target withString:(NSString *)replacement options:(NSStringCompareOptions)options range:(NSRange)searchRange
- (NSString )stringByReplacingCharactersInRange:(NSRange)range withString:(NSString )replacement


6. NSMutableString
由于 NSMutableString 是继承于 NSString，所以这里和 NSString 有些同样的方法就不重复写了。
- (void)replaceCharactersInRange:(NSRange)range withString:(NSString *)aString
- (void)insertString:(NSString *)aString atIndex:(NSUInteger)loc
- (void)deleteCharactersInRange:(NSRange)range


7. KVC
- (void)setValue:(id)value forKey:(NSString *)key
- (void)setValue:(id)value forKeyPath:(NSString *)keyPath
- (void)setValue:(id)value forUndefinedKey:(NSString *)key //这个方法一般用来重写，不会主动调用
- (void)setValuesForKeysWithDictionary:(NSDictionary *)keyedValues


8. NSAttributedString
- (instancetype)initWithString:(NSString *)str
- (instancetype)initWithAttributedString:(NSAttributedString *)attrStr
- (instancetype)initWithString:(NSString )str attributes:(NSDictionary<NSString ,id> *)attrs


9. NSMutableAttributedString
- (instancetype)initWithString:(NSString *)str
- (instancetype)initWithString:(NSString )str attributes:(NSDictionary<NSString ,id> *)attrs
```

AvoidCrash 可防止的 Crash 目前仅限以上 9 个类， 目前仍在更新中。



### 实现原理的异同
#### NullSafe
NSNull 发送给它的而它又无法处理的消息经过如下几步处理:

1. 创建一个方法缓存，这个缓存会缓存项目中类的所有类名。
2. 遍历缓存，寻找是否已经有可以执行此方法的类。
3. 如果有的话，返回这个 NSMethodSignature 。
4. 如果没有的话，返回 nil ， 接下来会走 forwardInvocation: 方法。
5. [invocation invokeWithTarget:nil] ; 将消息转发给 nil 。

在 OC 中，系统如果对某个实例发送消息之后，它（及其父类）无法处理（比如，没有这个方法等），系统就会发送 methodSignatureForSelector 消息，如果这个方法返回非空，那么就去执行返回的方法，如果为 nil，则发送 forwardInvocation 消息，这样就完成整个转发链了。


#### AvoidCrash
AvoidCrash 利用 `runtime` 技术对一些常用并且容易导致崩溃的方法进行处理，可以有效的防止崩溃。

1. 通过 category 给类添加方法用来替换掉原本存在潜在崩溃的方法。
2. 利用 runtime 方法交换技术，将系统方法替换成我们给类添加的新方法。
3. 利用异常的捕获来防止程序的崩溃，并且进行相应的处理。



### 两者的优势对比
#### 优势
##### NullSafe
1. 可自动将所有的 NSNull 对象转为 nil，直接防止空值导致的 Crash 。
2. 作为 Extension 使用方便，一劳永逸。

##### AvoidCrash
1. 可捕获常用的 Foundation 中的方法存在潜在崩溃风险。
2. 可定位到具体的 Crash 位置。
3. 可以获取崩溃日志的所有详细信息。
4. 可拓展性强。


####劣势
##### NullSafe
1. 仅仅局限于处理空对象，而 AvoidCrash 可捕获用的 Foundation 方法的 Crash 。
2. 可拓展性差。

##### AvoidCrash
1. 仅作用于常用的 Foundation 中的方法的 Crash 。
2. 目前版本并不是很稳定。



### 两者的使用方法
#### NullSafe
NullSafe 是一个 Category，使用起来非常方便，只要加入到了工程中就可以了。


#### AvoidCrash
1. 在 AppDelegate 的 didFinishLaunchingWithOptions 方法中添加如下代码，让 AvoidCrash 生效。
```objc
// 这句代码会让AvoidCrash生效，若没有如下代码，则AvoidCrash就不起作用
[AvoidCrash becomeEffective];
```

2. 如果想要获取崩溃日志的所有详细信息，需要注册通知：AvoidCrashNotification 。
```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    [AvoidCrash becomeEffective];
    
    //监听通知:AvoidCrashNotification, 获取AvoidCrash捕获的崩溃日志的详细信息
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(dealwithCrashMessage:) name:AvoidCrashNotification object:nil];
    return YES;
}
- (void)dealwithCrashMessage:(NSNotification *)note {
    //注意:所有的信息都在userInfo中
    //你可以在这里收集相应的崩溃信息进行相应的处理(比如传到自己服务器)
    NSLog(@"%@",note.userInfo);
}
```


### 总结建议
综上，个人更倾向于 AvoidCrash ，虽然目前还不是很完善，但也可捕获常用的 Foundation 中的方法存在潜在崩溃风险，相对来说， NullSafe 便有些局限。

在发布 APP 的时候再用上面介绍的方法来防止程序的崩溃，在开发阶段最好不用，能更好的发现应用的问题和不足。