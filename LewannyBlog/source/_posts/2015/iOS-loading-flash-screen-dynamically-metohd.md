---
title: iOS 简单而实用的动态加载闪屏图方法
date: 2015-07-17 08:42:13
tags: 动态闪屏
categories: Objective-C
---

iOS 开发中往往有动态更换闪屏图或者在闪屏图后加载广告的需求，如果是在闪屏后加载广告，在闪屏展示对应的广告页面即可。但是如果要动态更新闪屏图启是不可以的，如果要想实现这种效果，只能做出一种假象来，让人感觉你的启动图就是动态获取的。

实现的思路是先把启动图空白或者部分空白，然后用一个 VC 或者 UIimageview 来做动态获取服务器图片然后显示的功能，这个 VC 的界面一定要做的和你的启动页一模一样。然后在第一个 VC 中，设置代码加动画，设置请求下来的启动图什么的就都可以了。

<!--more-->

代码如下：
```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    MainViewController *mainVc = [[MainViewController alloc] init];
    UINavigationController *navi = [[UINavigationController alloc] initWithRootViewController:mainVc];
    self.window.rootViewController = navi;
    [self.window makeKeyAndVisible];
    
    // 在 window 上放一个 imageView
    UIImageView *imageView = [[UIImageView alloc]initWithFrame:[UIScreen mainScreen].bounds];
    imageView.image = [UIImage imageNamed:@"img_bg-1"];
    [self.window addSubview:imageView];
    
    // 执行动画
    [UIView animateWithDuration:2 animations:^{
        // 两秒内图片变大为原来的 1.3 倍
        imageView.transform = CGAffineTransformMakeScale(1.2,1.2);
        imageView.alpha = 0;
    } completion:^(BOOL finished) {
        // 动画结束，移除 imageView，呈现主界面
        [imageView removeFromSuperview];
    }];
    
    return YES;
}
```