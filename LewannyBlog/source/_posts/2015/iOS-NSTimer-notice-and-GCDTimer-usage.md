---
title: iOS NSTimer 注意事项及 GCD 定时器的使用
date: 2015-02-19 17:29:07
tags: NSTimer
categories: Objective-C
---


### NSTimer 定时器
---
#### NSTimer 的创建和使用
```objc
// 创建和开始
NSTimer *timer =  [NSTimer scheduledTimerWithTimeInterval:1.0
                                                   target:self
                                                   selector:@selector(beginCount)
                                                   userInfo:nil
                                                   repeats:YES];
```
注意：Timer 自动启动。`repeats` 参数表示是否循环使用定时器，NO 表示只调用一次。<font color=red>将计数器的 repeats 设置为 YES 的时候，self 的引用计数会加 1 。</font>因此可能会导致 self 不能 release，所以，必须在 viewWillAppear 的时候，将计数器 timer 关闭，否则可能会导致内存泄露。

<!--more-->

#### NSTimer的关闭
```objc
// 关闭
[timer invalidate];
timer = nil;
```
注意：一定要赋值为空，这样才是真的释放。

#### NSTimer的暂停和继续
```objc
// 暂停
[timer setFireDate:[NSDate distantFuture]];
// 继续
[timer setFireDate:[NSDate date]];
```

#### NSTimer的弊端和注意下事项
- 必须保证有一个活跃的 runloop，子线程的 runloop 是默认关闭的，这时如果不激活 runloop，performSelector 和 scheduledTimerWithTimeInterval 的调用将是无效的。
- NSTimer 的创建与撤销必须在同一个线程操作、performSelector 的创建与撤销必须在同一个线程操作。
- 内存管理有潜在泄露的风险。
- 如果不是通过 invalidate 来关闭是无法停止的，还有持有 self，造成对象无法释放。

**所以推荐用 dispatch 的 timer。**



### GCD 定时器
---
#### GCD 定时器的创建和执行
```objc
- (void)startGCDTimer{
    // GCD定时器
    static dispatch_source_t _timer;
    NSTimeInterval period = 1.0; //设置时间间隔
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    _timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
    dispatch_source_set_timer(_timer, dispatch_walltime(NULL, 0), period * NSEC_PER_SEC, 0); //每秒执行
    // 事件回调
    dispatch_source_set_event_handler(_timer, ^{
        // 在主线程中执行
        dispatch_async(dispatch_get_main_queue(), ^{   
            NSLog(@"Do Count"); 
        });
    });
    
    dispatch_resume(_timer);
}
```
**注意：**dispatch_source_t 必须是全局或 static 变量。

#### GCD 定时器的停止
```objc
-(void) stopGCDTimer{
    // 停止
    if(_timer){
        dispatch_source_cancel(_timer);
        _timer = nil;
    }
}
```

#### GCD 定时器的暂停和继续
```objc
-(void) pauseGCDTimer{
    // 暂停     
    if(_timer){
        dispatch_suspend(_timer);
    }
}
-(void) resumeGCDTimer{
    // 继续
    if(_timer){
        dispatch_resume(_timer);
    }
}
```