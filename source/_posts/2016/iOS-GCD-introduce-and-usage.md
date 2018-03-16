---
title: iOS 关于 GCD 的详细介绍和基本用法
date: 2016-06-20 21:13:16
tags: GCD
categories: Objective-C
---

### 什么是 GCD ？
---
#### GCD 的简介
`GCD 是 “Grand Central Dispatch” 的简称`，是一套底层 API ，提供了一种新的方法来进行并发程序编写。从基本功能上讲，GCD 有点像 `NSOperationQueue` ，他们都允许程序将任务切分为多个单一任务然后提交至工作队列来并发地或者串行地执行。GCD 比之 NSOpertionQueue 更底层更高效，并且它不是 Cocoa 框架的一部分。

GCD 是为多核的并行运算提出的解决方案，除了代码的平行执行能力，GCD 还提供高度集成的事件控制系统。可以设置句柄来响应文件描述符、mach ports（Mach port 用于 OS X 上的进程间通讯）、进程、计时器、信号、用户生成事件。这些句柄通过 GCD 来并发执行。

<!--more-->

GCD 的 API 很大程度上`基于 block` ，当然，GCD 也可以脱离 block 来使用，比如使用传统 c 机制提供函数指针和上下文指针。实践证明，当配合 block 使用时，GCD 非常简单易用且能发挥其最大能力。


#### GCD 的工作原理：
让程序平行排队的特定任务，根据可用的处理资源，安排他们在任何可用的处理器核心上执行任务。一个任务可以是一个函数 (function) 或者是一个 block。GCD 的底层依然是用线程实现，不过这样可以让程序员不用关注实现的细节。


#### GCD 的优势
- 易用： GCD 比之 `thread 更加简单易用`。由于 GCD 基于 work unit 而非像 thread 那样基于运算，所以 GCD 可以控制诸如等待任务结束、监视文件描述符、周期执行代码以及工作挂起等任务。基于 block 的血统导致它能极为简单得在不同代码作用域之间传递上下文。

- 效率： GCD 被实现得如此轻量和优雅，使得它在很多地方比之专门创建消耗资源的线程更实用且快速。这关系到易用性：导致 GCD 易用的原因有一部分在于你可以不用担心太多的效率问题而仅仅使用它就行了。

- 性能： GCD 自动根据系统负载来增减线程数量，这就减少了上下文切换以及增加了计算效率。


#### GCD 的使用步骤和核心概念
##### 核心概率有 2 个
- 队列：用来存放任务
- 任务：执行什么操作

##### 使用步骤有2步
- 定制任务
- 确定想做的事情


### GCD 的核心工作原理
---
####  任务和队列
##### 任务
即操作，你想要干什么，说白了就是一段代码，在 GCD 中就是一个 Block，所以添加任务十分方便。任务有两种执行方式： `同步执行` 和 `异步执行`，`同步（sync）` 和`异步（async）` 的主要区别在于会不会阻塞当前线程，直到 Block 中的任务执行完毕。

- 同步执行：它会阻塞当前线程并等待 Block 中的任务执行完毕，然后当前线程才会继续运行。
- 异步执行：当前线程会直接往下执行，它不会阻塞当前线程。

##### 队列
用于存放任务。系统提供了两个队列，一个是 `MainDispatchQueue`，一个是 `GlobalDispatchQueue`， 即 `串行队列和并行队列`。

- 串行队列：放到串行队列的任务，GCD 会 FIFO（先进先出） 地取出来一个，执行一个，然后取下一个，这样一个一个的执行。会将任务插入主线程的 RunLoop 当中去执行，我们可以使用它来更新 UI。

- 并行队列：放到并行队列的任务，GCD 也会 FIFO 的取出来，但不同的是，它取出来一个就会放到别的线程，然后再取出来一个又放到另一个的线程。这样由于取的动作很快，忽略不计，看起来，所有的任务都是一起执行的。不过需要注意，GCD 会根据系统资源控制并行的数量，所以如果任务很多，它并不会让所有任务同时执行。有高、默认、低和后台 4 个优先级。


#### 创建队列
##### 主队列：
这是一个特殊的串行队列。什么是主队列，大家都知道吧，它用于刷新 UI，任何需 要刷新 UI 的工作都要在主队列执行，所以一般耗时的任务都要放到别的线程执行。
```objc
dispatch_queue_t mainQueue = dispatch_get_main_queue();
```

##### 全局并行队列
只要是并行任务一般都加入到这个队列。这是系统提供的一个并发队列。
```objc
dispatch_queue_t globalQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

##### 自己创建的队列
可以是`串行队列`，也可以创建`并行队列`。看下面的代码，它有两个参数，第一个为标识，第二个才是最重要的。传入 `DISPATCH_QUEUE_SERIAL` 或 `NULL` 表示创建串行队列。传入 `DISPATCH_QUEUE_CONCURRENT` 表示创建并行队列.
```objc
// 串行队列
dispatch_queue_t serialQueue = dispatch_queue_create("SerialQueue", DISPATCH_QUEUE_SERIAL);
// 并行队列
dispatch_queue_t concurrentQueue = dispatch_queue_create("ConcurrentQueue", DISPATCH_QUEUE_CONCURRENT);
```


#### 创建任务
##### 同步任务
会`阻塞当前线程 (Sync)` 。
```objc
dispatch_sync(queue, ^{
    //code here
    NSLog(@"%@", [NSThread currentThread]);
});
```

##### 异步任务：
不会阻塞当前线程 (Async) 。
```objc
dispatch_async(<#queue#>, ^{
    //code here
    NSLog(@"%@", [NSThread currentThread]);
});
```


#### 线程
`dispatch_asyn` 和 `dispatch_sync` 添加任务到 dispatch 队列时，是否创建线程呢，那么创建线程是创建一个呢还是多个呢?

1. dispatch_sync 添加任务到队列，不会创建新的线程都是在当前线程中处理的。无论添加到串行队列里或者并行队列里，都是串行效果，因为这个方法是等任务执行完成以后才会返回。

2. dispatch_async 添加任务到
- mainQueue 不创建线程，在主线程中串行执行。
- globalQueue 和 并行队列：根据任务系统决定开辟线程个数。
- 串行对列：创建一个线程：串行执行。

具体看下表：

|  | 同步执行 | 异步执行 |
| :-------------: |:-------------:| :-------------:|
| 串行队列 | 当前线程，一个一个执行 | 其他线程，一个一个执行 |
| 并行队列 | 当前线程，一个一个执行 | 开很多线程，一起执行 |



### GCD 的基本使用
#### 延迟执行 dispatch_after
不需要再写方法，且它还传递了一个队列，我们可以指定并安排其线程。如果队列是主队列，那么就在主线程执行，如果队列是并发队列，那么会新开启一个线程，在子线程中执行。
```objc
NSLog(@"打印当前线程---%@",  [NSThread currentThread]);
// 延迟执行, 这段代码将会在2秒后将任务插入 RunLoop 当中
dispatch_queue_t queue= dispatch_get_main_queue();
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), queue, ^{
    NSLog(@"主队列--延迟执行------%@",[NSThread currentThread]);
});
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_after.jpeg" width=600></img>
  
  
#### 一次性执行代码 dispatch_once
整个程序运行过程中，只会执行一次。`单例对象创建`的方法。
```objc
// 一次性执行：使用 dispatch_once 一次性代码
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    NSLog(@"该行代码只执行一次");
});
```


#### 多线程编程
上面已经提过队列一般就是系统的 `主队列` 和 `全局队列` 还有自己`手动创建的串行队列和并行队列`:
```objc
dispatch_queue_t serialQueue = dispatch_queue_create("SerialQueue", DISPATCH_QUEUE_SERIAL);  // 串行队列
dispatch_queue_t concurrentQueue =   // 并行队列dispatch_queue_create("ConcurrentQueue",DISPATCH_QUEUE_CONCURRENT);
dispatch_queue_t mainQueue = dispatch_get_main_queue();  // 主队列
dispatch_queue_t globalQueue = dispatch_get_global_queue(0, 0);  // 全局队列
```

方法有两个 `dispatch_sync` 和 `dispatch_async` 。
```objc
dispatch_sync  // 立刻返回的，也就是说把 block 内容加到相应队列里后会立马返回。
dispatch_async  // 要等到加到队列里执行完之后才会返回。
```

这时候总共有 2*4 = 8 种组合，接下来我们来说一下这八种组合。

##### 1. 串行队列同步执行
第一种方式可见，串行队列是在主线程里完成的，因为是串行队列，所以打印 %d 是有顺序的。
```objc
for(int i = 0; i < 3; i++){
    dispatch_sync(serialQueue, ^{
        NSLog(@"串行队列同步执行:%d\n mainThread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_syncSerialQueue.jpeg" width=600></img>
  
  
##### 2. 并行队列同步执行
这种方式可以发现同样还是在主线程里执行并行队列，(虽然是并行队列，但这时候依然在同一个线程里执行)。
```objc
for(int i = 0; i < 3; i++){
    dispatch_sync(concurrentQueue, ^{
        NSLog(@"并行队列同步执行:%d\n mainThread:%@",i,[NSThread currentThread]);     
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_syncConcurrentQueue.jpeg" width=600></img>
  
##### 3. 主队列同步执行(线程阻塞)
打印结果: 发现只打印了一条，同步执行, 发现`主线程被堵塞`。因为此时 mainQueue 里存在任务 1，同步线程任务，当执行 dispatch_sync 时，把打印任务 2 加入主队列，想要打印 2 必须等之前所有的任务都执行完成，这时候因为主队列里有同步线程任务，这时候相当于自己在等自己执行完成，进入死循环。
```objc
for(int i = 0; i < 3; i++){
    NSLog(@"任务 1");
    dispatch_sync(mainQueue, ^{
        NSLog(@"任务2: 主队列同步执行:%d\n mainThread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_syncMainQueue.jpeg" width=600></img>


##### 4. 全局队列同步执行
打印结果可以看出依然是主线程。
```objc
for(int i = 0; i < 3; i++){
    dispatch_sync(globalQueue, ^{
        NSLog(@"全局队列同步执行:%d\n mainThread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_syncGlobalQueue.jpeg" width=600></img>
  
  
##### 5. 串行队列异步执行
可以看出创建了一个线程, 在串行队列里串行执行的 `sleep 函数延时执行`: `[NSThread sleepForTimeInterval:1.0f]` ，此方式在主线程和子线程中均可执行，是一种阻塞的执行方式，建方放到子线程中，以免卡住界面。
```objc
for (int i = 0; i < 3; i++) {
    dispatch_async(chuanxingduilie, ^{
        f (i == 1) {
            sleep(2);
        }
        NSLog(@"串行队列异步执行: %d\n Thread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_asyncChuanxingduilie.jpeg" width=600></img>


##### 6. 并行队列异步执行
可以看出创建了多个线程，任务执行顺序不一定（时间差不多的话），这和 cpu 的处理和性能有关。
```objc
for (int i = 0; i < 3; i++) {
    dispatch_async(bingxingduilie, ^{
        if (i==1) {
            sleep(2);
        }
        NSLog(@"并行队列异步执行: %d\n Thread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_asyncBingxingduilie.jpeg" width=600></img>
  

##### 7. 主队列异步执行
可以看出这种情况还是在当前线程（主线程）环境中执行，并不创建线程，因为是在主队列里，顺序执行。
```objc
for (int i = 0; i < 3; i++) {
    dispatch_async(mainQueue, ^{
        if (i==1) {
            sleep(3);
        }
        NSLog(@"主队列异步执行: %d\n Thread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_asyncMainQueue.jpeg" width=600></img>
  
 
##### 8. 全局队列异步执行
可以看出创建了多个线程，执行顺序并不一定。
```objc
for (int i = 0; i < 3; i++) {
    dispatch_async(globalQueue, ^{
        if (i==1) {
            sleep(3);
        }
        NSLog(@"全局队列异步执行: %d\n Thread:%@",i,[NSThread currentThread]);
    });
}
```
  <img src="http://omkug1guu.bkt.clouddn.com/iOS-GCD-introduce-and-usage/dispatch_asyncGlobalQueue.jpeg" width=600></img>
  

#### 队列组 dispatch_group
队列组可以将很多队列添加到一个组里，这样做的好处是，当这个组里所有的任务都执行完了，列组会通过一个方法通知我们，这是一个很实用的功能。
```objc
// dispatch_group
dispatch_queue_t globalQueue1 = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_group_t group = dispatch_group_create();
    dispatch_group_async(group, globalQueue1, ^{
        // sleep(1);
        NSLog(@"1");
    });
    dispatch_group_async(group, globalQueue1, ^{
        // sleep(2);
        NSLog(@"2");
    });
    dispatch_group_async(group, globalQueue1, ^{
        // sleep(3);
        NSLog(@"3");
    });
    // 保证此处为最后执行
    dispatch_group_notify(group, globalQueue1, ^{
        NSLog(@"Over!");
});
```

#### dispatch_barrier_async
下面内容翻译自官方文档：
> 一个 dispatch barrier 允许在一个并发队列中创建一个同步点。当在并发队列中遇到 barrier，他会延迟执行barrier的block，等待所有在barrier之前提交的blocks执行结束。 这时，barrier block 自己开始执行。 之后，队列继续正常的执行操作。
```objc
// dispatch_barrier_async
dispatch_queue_t concurrentQueue = dispatch_queue_create("my.concurrent.queue", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-1");
});
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-2");
});
// 保证 1+2 在执行完 1 和 2 的时候执行
dispatch_barrier_async(concurrentQueue, ^(){
    NSLog(@"dispatch-barrier-1+2");
});
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-3");
});
// 保证 1+2+3 在执行完 1 和 2 和 3 的时候执行
dispatch_barrier_async(concurrentQueue, ^{
    NSLog(@"dispatch-barrier-1+2+3");
});
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-4");
});
```


#### 暂停和恢复
可以使用 `dispatch_suspend(queue)` 可以暂停队列中任务的执行，使用 `dispatch_result(queue)` 可以继续执行被暂停的队列。



### 使用过程中的注意事项
---
#### dispatch_once_t 必须是全局或 static 变量
非全局或非 static 的 dispatch_once_t 变量在使用时会导致非常不好排查的 bug，正确的如下：
```objc
static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        // code
});
```
其实就是`保证 dispatch_once_t 只有一份实例`。


#### dispatch_after 是延迟提交，不是延迟运行
官方文档的说明：
> Enqueue a block for execution at the specified time.
Enqueue，就是入队，指的就是将一个 Block 在特定的延时以后，加入到指定的队列中，不是在特定的时间后立即运行。


#### 正确创建 dispatch_time_t
用 dispatch_after 的时候就会用到 dispatch_time_t 变量，但是如何创建合适的时间呢？答案就是用dispatch_time 函数，其原型如下：
```objc
dispatch_time_t dispatch_time ( dispatch_time_t when, int64_t delta);
```

第一个参数一般是 `DISPATCH_TIME_NOW`，表示从现在开始，那么第二个参数就是真正的延时的具体时间。这里要特别注意的是，delta 参数是 “纳秒”，延时 1 秒 delta 应该是 “1000000000” ，所以理所当然系统提供了常量:
```objc
#define NSEC_PER_SEC 1000000000ull  // NSEC_PER_SEC，每秒有多少纳秒。
#define USEC_PER_SEC 1000000ull  // USEC_PER_SEC，每秒有多少毫秒。
#define NSEC_PER_USEC 1000ull  // NSEC_PER_USEC，每毫秒有多少纳秒。
```

所以，延时 1 秒可以写成如下几种：
```objc
dispatch_time(DISPATCH_TIME_NOW, 1 * NSEC_PER_SEC);
dispatch_time(DISPATCH_TIME_NOW, 1000 * USEC_PER_SEC);
dispatch_time(DISPATCH_TIME_NOW, USEC_PER_SEC * NSEC_PER_USEC);
```


#### dispatch_suspend != 立即停 止队列的运行
dispatch_suspend，dispatch_resume 提供了 “挂起、恢复” 队列的功能，简单来说，就是可以暂停、恢复队列上的任务。但是 dispatch_suspend 并不会立即暂停正在运行的 block，而是在当前 block 执行完成后，暂停后续的 block 执行。


#### 避免死锁
##### 1. dispatch_sync 导致的死锁
涉及到多线程的时候，不可避免的就会有 死锁 这个问题，在使用 GCD 时，往往一不小心，就可能造成死锁。

##### 2. dispatch_apply 导致的死锁
dispatch_apply 的作用是在一个队列（串行或并行）上“运行”多次 block，其实就是简化了用循环去向队列依次添加 block 任务。dispatch_apply 也会导致的死锁。看如下例子：
```objc
// 这端代码只会输出“apply loop: 1”
// 所以，一定要避免dispatch_apply的嵌套调用。dispatch_queue_t queue = dispatch_queue_create("me.tutuge.test.gcd", DISPATCH_QUEUE_SERIAL);    
dispatch_apply(3, queue, ^(size_t i) {
    NSLog(@"apply loop: %zu", i);
    // 再来一个dispatch_apply就会导致死锁。
    dispatch_apply(3, queue, ^(size_t j) {
        NSLog(@"apply loop inside %zu", j);
    });
});
```


#### 灵活使用 dispatch_group
很多时候我们需要等待一系列任务（block）执行完成，然后再做一些收尾的工作。如果是有序的任务，可以分步骤完成的，直接使用串行队列就行。但是如果是一系列并行执行的任务呢？这个时候，就需要 `dispatch_group`。dispatch_group 的使用分如下几步：

1. 创建 dispatch_group_t
2. 添加任务（block）
3. 添加结束任务（如清理操作、通知UI等）


#### 使用 dispatch_barrier_async 和 dispatch_barrier_sync 的注意事项
dispatch_barrier_async 的作用就是向某个队列插入一个 block，当目前正在执行的 block 运行完成后，阻塞这个 block 后面添加的 block，只运行这个 block 直到完成，然后再继续后续的任务。

值得注意的是：

- dispatch barrier (a)sync 只在自己创建的并发队列上有效。
- 在全局 (Global) 并发队列、串行队列上，效果跟dispatch_(a)sync 效果一样。
- 在串行队列上跟 dispatch_(a)sync 效果一样，那就要小心线程锁死。
