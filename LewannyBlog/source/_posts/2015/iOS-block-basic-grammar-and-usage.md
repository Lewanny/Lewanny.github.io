---
title: iOS 中 Block 的基本语法和使用
date: 2015-09-24 19:23:33
tags: block
categories: Objective-C
---
代码块 Block 是苹果在 iOS4 开始引入的对 C 语言的扩展，用来实现匿名函数的特性。Block 是一种特殊的数据类型，其可以正常定义变量、作为参数、作为返回值。特殊地，Block 还可以保存一段代码，在需要的时候调用。目前 Block 已经广泛应用于 iOS 开发中，常用于 GCD、动画、排序及各类回调。

Block 在我们的实际开发中太常用了，个人觉得很好用，无论是界面传值还是当做参数都十分的灵活。例如 GCD、AFNetworking 的回调等都是用 Block 实现的，且 Apple 官方的很多 API 都由 Delegate 写法换成了 Block 。

<!--more-->

### Block 是什么？
那么，Block 到底是什么呢？用一句话来概括就是`带有自动变量的匿名函数`。

#### 匿名函数
`匿名函数`顾名思义就是不带名字的函数，C 语言中不允许该类函数的存在，而在 OC 的 Block 中则是可以使用指针来直接调用一个函数的。

#### 自动变量
自动变量在 Block 中的具体表现就是`截获自动变量`。
```objc
int a = 0;
void(^blockLog)() = ^{
    NSLog(@"Input a = %d", a);
};
a = 10;
blockLog();
// 打印结果：Input a = 0
```
上面的代码虽然我们在调用 blockLog 前改变了变量 a 的值，但是输出的值还是 blockLog 在编译时 a 的值。所以截获自动变量的概念就是：**在Block中会保存变量的值，而不会随变量的值的改变而改变**。

我们再来看下面的代码。
```objc
int a = 0;
void(^blockLog)() = ^{
    a = 10;
    NSLog(@"Input a = %d", a);
};
```

上面的代码会报错，编译器会提示不能再 Block 中改变变量的值。因为在 Block 截获了变量的瞬间值后就不能再改变改变量的值，如果想在 Block 中改变该变量的值，那么我们需要在变量声明的时候加上 __Block 修饰符。如下：
```objc
__block int a = 0;
void(^blockLog)() = ^{
    a = 10;
    NSLog(@"Input a = %d", a);
};
blockLog();
// 打印结果：Input a = 10
```

但是下面的情况是允许的：
```objc
NSMutableArray *arrayInsert = [NSMutableArray array];
void(^blockLog)() = ^{
    [arrayInsert addObject:@"Obj"];
    NSLog(@"array = %@", arrayInsert);
};
blockLog();
```

因为我们只是对截获的变量进行了操作，而没有进行赋值，所以对于截获变量，可以进行操作而不可以进行赋值。

还有一点需要注意，在 Block 中不支持对 C 语言数组进行操作。



### Block 的语法和分类
#### Block 的语法
下面是一个完整的 Block 写法：
```objc
^int (NSString *str1, NSString *str2) {
	return [str1 intValue] + [str2 intValue];
}
```

- “^” 这个符号用来表示这是一个 `Block` 。
- int 表示`返回值`。（可省略或 void）
- (NSString str1, NSString str2) 这个括号中是 Block 的参数，即`参数列表`，语法和 C 语言类似。（没有时也可省略）

#### Block 变量
那么为什么需要 Block 变量？我们可以这样理解，我们可以通过这个 Block 变量来获取 Block 的指针，然后通过这个指针就可以来使用 Block 函数。我们先来看一下如何声明一个 Block 变量：
```objc
int (^blockExp)(NSString *str1,NSString *str1);
```

对照前面的 Block 函数其含义分别为：

- 返回值
- 变量名
- 参数列表

然后我们用上面讲到的 Block 语法来对这 Block 变量进行赋值，我们就可以将这个 Block 变量当作 C 语言函数来使用了。

```objc
int (^blockExp)(NSString *str1, NSString *str2);
blockExp = ^(NSString *str1, NSString *str2) {
    return [str1 intValue] + [str2 intValue];
};
NSLog(@"Result = %d", blockExp(@"1", @"2"));
// 打印结果：Result = 3
```

#### 无参无返的 Block
无参数无返回值的 Block 。
```objc
void (^block1)() = ^() {
    
};
block1()
```

#### 有参无返的 Block
无参数无返回值的 Block 。
```objc
void (^block2)(NSString *) = ^(NSString *str) {
    
};
block2(@"Obj");
```

#### 有参有返的 Block
无参数有返回值的 Block 。
```objc
int (^block3)(int num) = ^(int num) {
    return num+1;
};
NSLog(@"Result = %d", block3(10));
// 输出结果：Result = 11
```


### Block 的实际应用
#### Block 结合 typedef 使用
在实际使用 Block 的过程中，我们可能需要重复地声明多个相同返回值相同参数列表的 Block 变量，如果总是重复地编写一长串代码来声明变量会非常繁琐，所以我们可以使用 typedef 来定义 Block 类型。
```objc
// 定义一种无返回值有参数的 Block 类型
typedef void (^SayHello)(NSString *str);
// 可以像 OC 中声明变量一样使用 Block 类型 SayHello 来声明变量并调用
SayHello hello = ^(NSString *saySrr){
    NSLog(@"%@", saySrr);
};
hello(@"Hello World");
    
// 打印结果：Hello World
```

#### Block 作为属性
```objc
// 定义一种有参有返的 Block 
typedef NSString *(^MyBlock)(int a);
// 定义一个 MyBlock 属性
@property (nonatomic,copy)MyBlock block;
```

#### Block 作为函数参数
```objc
// 定义一个 Block 
typedef void (^MyBlock)(UIColor* aColor);
```

这样我们就可以使用 MyBlock 来表示这个 Block，将 MyBlock 加入到函数参数中来声明一个函数。
```objc
- (void)setMyBlock:(MyBlock)block {
    
    block([UIColor redColor]);
}
```

可在任意地方调用这个函数。
```objc
[secondVc setMyBlock:^NSString *(UIColor* aColor) {
    NSLog(@"%@", aColor);
    wself.view.backgroundColor = aColor;
    return @"";
}];
```

我们平时锁使用的许多回调当中大多都是这样的形式，其较多的就是网络请求回调了，我们只需要调用方法，然后在回调当中就可以对结果进行操作，很多苹果自己写的 API 都是使用了这样的方法。这样做的好处就是形式上十份简洁，当然像这种地方你使用 delegate 肯定也是可以的，但是表现上就没有 Block 那么简洁，使用起来也没有 Block 那么方便。

#### 使用 Block 在页面中反向传值
Blcok 是一个匿名函数，同时也是一个指针，那么使用 Block 就可以弥补在 iOS 中函数传递的功能。
在页面 B 中定义一个 Block 并声明一个 Block 变量。
```objc
// 声明部分
typedef void(^BlockChange)(NSString *titleStr, UIColor *bgColor);
@property(nonatomic, copy)BlockChange blockChange;
- (void)setChangeColorBlock:(BlockChange) block;
// 实现部分
- (void)setChangeColorBlock:(BlockChange) block {
    self.blockChange = block;
}
```

在页面 A 中的代码。
```objc
SecondViewController *secondVc = [[SecondViewController alloc] init];
__weak FirstViewController * wself = self;
[secondVc setChangeColorBlock:^(NSString *titleStr, UIColor *bgColor) {
    wself.view.backgroundColor = bgColor;
    wself.tabBarItem.title = titleStr;
}];
```

在页面 B 的任意适当位置调用 block 变量。
```objc
self.blockChange(@"传递颜色", [UIColor grayColor]);
```

这样 @”传递颜色” 和 [UIColor grayColor] 就从界面 B 传到了界面 A 。



### 注意事项
#### 使用 __block 修改局部变量

Block 可以访问局部变量，但是不能修改。如果想要修改局部变量，需要使用 __block 来修饰变量。
```objc
__block int multiplier = 7;
int (^myBlock)(int) = ^(int num) {
      multiplier ++;//这样就可以了
      return num * multiplier;
};
```

但是如果局部变量是`数组字典或者指针`的时候，只 copy 了这个指针，两个指针指向同一个地址，block 只修改指针上的内容。
```objc
NSMutableArray *arrayInsert = [NSMutableArray array];
void(^blockLog)() = ^{
    [arrayInsert addObject:@"Obj"];
    NSLog(@"array = %@", arrayInsert);
};
```

例子中的 arrayInsert 是一个指针，指向一个可数组。在 block 里面，并没有修改这个指针，而是修改了这个指针指向的数组。换句话说，指针保存的是一块内存区域的地址，在block 里，并没有改变这个地址，而是读取出这个地址，然后去操作这块地址空间的内容，这是允许的。

因为声明 block 的时候实际上是把当时的临时变量又复制了一份，在 block 里即使修改了这些复制的变量，也不影响外面的原始变量。即所谓的闭包。但是当变量是一个指针的时候，block里只是复制了一份这个指针，两个指针指向同一个地址。所以，在block里面对指针指向内容做的修改，在block外面也一样生效。


#### 使用 __week 修改局部变量

如果在 Block 中使用附有 `__strong` 修饰符的对象类型自动变量，那么当 Block 从栈复制到堆时，改对象为 Block 所有。这样容易`引起循环引用`，从而发生内存泄漏。然而我们只需要保证当前控制器也就是 self 在需要释放的时候正确释放就可以。
```objc
__weak FirstViewController * wself = self;
```

我们定义一个 `wself 变量并使用 __weak 修饰符`，在 Block 代码块中，所有需要 self 的地方都用 wself 来替代。这样就不会增加引用计数，所以 Block 持有 self 对象就不会造成循环引用，从而造成内存泄漏。

注意：在使用 block 前需要对 block 指针做判空处理，如果是MRC的编译环境下，要先 release 掉 block 对象。将 block 赋值为空，是解掉循环引用的重要方法。
