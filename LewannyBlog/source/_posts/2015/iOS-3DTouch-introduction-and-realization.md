---
title: iOS 3D Touch 功能的介绍和实现
date: 2015-12-11 22:26:52
tags: 3D Touch
categories: Objective-C
---
3D Touch 是苹果在 2015 年秋季发布会上发布的 iPhone 6S 的新功能，除了类似 Apple Watch、MacBook 触摸板的标准、深压体验之外， iPhone 6s 的触摸屏还将新增一种新层次的维度，实现更多操作体验。


### 3D Touch 是什么
#### 3D Touch 的具体功能
3D Touch 是一种可以让你与手机进行互动的全新方式。新的 iPhone 6S 能够感应你按压屏幕的力度。除了轻点、轻扫、双指开合这些熟悉的 Multi‑Touch 手势之外，3D Touch 还带来 Peek 和 Pop，且当你使用 3D Touch 时，iPhone 将回以轻微的触感，让你不仅能够看到按下屏幕的操作效果，还能感觉得到。

<!--more-->

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-3DTouch-introduction-and-realization/3D%20Touch.png" width="720" height="500"></img>
  
具体功能是，用力按一个图标会弹出一层半透明菜单，里面包含了该应用下的一些快捷操作，看起来类似 PC 上的右键。3D Touch 的功能很强大，Peek 和 Pop 让你能够预览所有类型的内容，甚至可对内容进行操作，却不必真的打开它们。例如轻点电话就可以查看最近联系人，按压相机可以快速自拍，按压图片库可以快速浏览大图等。


#### 开发环境和调试设备
Apple 不仅推出了 3D Touch 功能，还开放其 API 的调用接口。

- 开发环境：Xcode7 或以上版本。
- 调试设备：iOS9 或以上系统版本，iPhone6s 或以上设备。


#### 主要功能模块
3D Touch 功能主要分为两大块：

- 主屏幕 Icon 上的快捷标签。（Home Screen Quick Actions
- Peek（预览）和 Pop （跳至预览的详细界面）



### 3D Touch 的实现
#### 如何让你的模拟器支持 3D Touch
3D Touch的必要条件是 iPhone 6s 或以上、iOS 9 或以上、 Xcode 7 或以上版本，没有 iPhone 6s 的话也没关系，github 上有人提供了这样的一个可以让我们在模拟器上进行 3D Touch 的效果测试的插件，安装和使用方法参见 git 主页里的介绍。
[SBShortcutMenuSimulator](https://github.com/DeskConnect/SBShortcutMenuSimulator)

#### 如何判断设备是否支持 3D Touch
```objc
if (self.traitCollection.forceTouchCapability == UIForceTouchCapabilityAvailable) {
    // 支持 3D Touch
}
```
不能使用判断手机是否为 6S 或 6SP，是因为如果用户在设置 -> 通用 -> 3D Touch 中将该功能关闭的话，同样 forceTouch 是不可用的。另外可在 traitCollectionDidChange: 代理中监听 forceTouchCapability 的变化。


#### 主屏幕按压应用图标展示快捷选项（即 Home Screen Quick Actions）
一个应用最多可以有 4 个快捷选项标签，iOS 9 为我们提供了 2 种方式来开发按压应用图标展示快捷选项功能（Home Screen Quick Actions），即动态标签和静态标签。

##### 静态标签
在 info.plist 文件中手动加入 UIApplicationShortcutItems 选项。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-3DTouch-introduction-and-realization/PlistAdd.png" width="720" height="500"></img>
  
- UIApplicationShortcutItems：数组中的每个元素就是每一个快捷选项标签（最多 4 个）。
- UIApplicationShortcutItemTitle：标签的标题（必填）
- UIApplicationShortcutItemType：标签的唯一标识，用作区分（必填）
- UIApplicationShortcutItemIconType：标签系统图标的类型，如搜索、定位、分享等，详见 enum UIApplicationShortcutIconType（选填）
- UIApplicationShortcutItemIconFile：自定义标签图片（可选）
- UIApplicationShortcutItemSubtitle：标签的副标题（可选）
- UIApplicationShortcutItemUserInfo：字典信息，可用于传值等（可选）

##### 动态标签
在 AppDelegate.m 文件中调用如下方法：
```objc
//创建应用图标上的3D touch快捷选项
- (void)creatShortcutItem {
    //创建系统风格的icon
    UIApplicationShortcutIcon *icon = [UIApplicationShortcutIcon iconWithType:UIApplicationShortcutIconTypeShare];
    
    // 创建自定义图标的icon
    // UIApplicationShortcutIcon *icon2 = [UIApplicationShortcutIcon iconWithTemplateImageName:@"分享.png"];
    
    //创建快捷选项
    UIApplicationShortcutItem * item = [[UIApplicationShortcutItem alloc]initWithType:@"com.mycompany.myapp.share" localizedTitle:@"分享" localizedSubtitle:@"分享副标题" icon:icon userInfo:@{@"key2" : @"value2"}];
    
    //添加到快捷选项数组
    [UIApplication sharedApplication].shortcutItems = @[item];
}
```

##### 点击快捷选项标签进入应用的响应
主屏幕 icon 上的快捷标签点击进入页面的实现有点类似消息通知的实现方式，需要增加两处代码：首次启动 App （杀死后）和 App 从后台进入到前台启动（未被杀死）。

首次启动的响应：
```objc
// App 首次启动的响应方法
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    MainViewController *mainView = [[MainViewController alloc] init];
    UINavigationController *mainNavi = [[UINavigationController alloc] initWithRootViewController:mainView];
    self.window.rootViewController = mainNavi;
    [self.window makeKeyAndVisible];
    
    //创建应用图标上的3D touch快捷选项
    [self creatShortcutItem];
    
    
    UIApplicationShortcutItem *shortcutItem = [launchOptions valueForKey:UIApplicationLaunchOptionsShortcutItemKey];
    
    // 如果是从快捷选项标签启动 App，则根据不同标识执行不同操作，然后返回 NO，防止调用
    // - (void)application:(UIApplication *)application performActionForShortcutItem:(UIApplicationShortcutItem *)shortcutItem completionHandler:(void (^)(BOOL))completionHandler
    if (shortcutItem) {
        //判断设置的快捷选项标签唯一标识，根据不同标识执行不同操作
        if([shortcutItem.type isEqualToString:@"com.lewanny.button"]){
            // 点击第一个按钮
            NSLog(@"点击了 -> 第一个按钮");
        } else if ([shortcutItem.type isEqualToString:@"com.lewanny.search"]) {
            // 进入搜索界面
            NSLog(@"点击了 -> 搜索按钮");
        } else if ([shortcutItem.type isEqualToString:@"com.lewanny.location"]) {
            // 进入定位界面
            NSLog(@"点击了 -> 定位按钮");
        }else if ([shortcutItem.type isEqualToString:@"com.lewanny.share"]) {
            // 进入分享页面
            NSLog(@"点击了 -> 分享按钮");
        }
        
        return NO;
    }
    
    return YES;
}
```

后台启动的响应：
```objc
// 如果 App 没被杀死，点开 Touch 后台启动会调用该代理方法
- (void)application:(UIApplication *)application performActionForShortcutItem:(UIApplicationShortcutItem *)shortcutItem completionHandler:(void (^)(BOOL))completionHandler {
    
    if (shortcutItem) {
        //判断设置的快捷选项标签唯一标识，根据不同标识执行不同操作
        if([shortcutItem.type isEqualToString:@"com.lewanny.button"]){
            // 点击第一个按钮
            NSLog(@"点击了 -> 第一个按钮");
        } else if ([shortcutItem.type isEqualToString:@"com.lewanny.search"]) {
            // 进入搜索界面
            NSLog(@"点击了 -> 搜索按钮");
        } else if ([shortcutItem.type isEqualToString:@"com.lewanny.location"]) {
            // 进入定位界面
            NSLog(@"点击了 -> 定位按钮");
        }else if ([shortcutItem.type isEqualToString:@"com.lewanny.share"]) {
            // 进入分享页面
            NSLog(@"点击了 -> 分享按钮");
        }
    }
    
    if (completionHandler) {
        completionHandler(YES);
    }
}
```


#### peek（展示预览）和 pop（跳页至预览的界面）的实现
peek：当你用力按下屏幕按到一定程度时，系统会弹出一个预览视图，这个过程就称之为 peek。pop：再用力按下去就会展开到预览视图的控制器，这过程就是 pop 。

##### 注册 peek 和 pop
首先给 view 注册 3DTouch 的 peek 和 pop 功能。（在需要使用该功能的个页面上注册）
```objc
// 注册 3D Touch，先判断是否可用
if (self.traitCollection.forceTouchCapability == UIForceTouchCapabilityAvailable){
  [self registerForPreviewingWithDelegate:self sourceView:self.locationView];
    NSLog(@"3D Touch 可用");
  }else{
    NSLog(@"3D Touch 无效");
}
```

##### 继承协议
需要继承协议 `UIViewControllerPreviewingDelegate` 。

##### 代理方法
实现 `UIViewControllerPreviewingDelegate` 的代理方法。
```objc
// peek 手势弹出的预览视图
// previewingContext: 执行 peek 的上下文对象。
// location: 按压位置再 souceView 上的点，可以理解为手指在屏幕上的按压点。
- (nullable UIViewController *)previewingContext:(id<UIViewControllerPreviewing>)previewingContext viewControllerForLocation:(CGPoint)location {
    
    // 获取sourceView 即注册时传入的sourceView，一般为控制器的view
    UIView *pressView = [previewingContext sourceView];
    
    // 然后判断按压点是否在某个控件的 frame 内，
    /*
     这里有个涉及到坐标系转换的细节问题
     如果self.locationView不是sourceView的直接子控件，那么我们需要把point转换到self.locationView的父控件的坐标系中，代码如下：
     point = [self.locationView.superView convertPoint:point fromView:sourceView];
     若缺少这段代码，你会发现按压位置错乱的bug。
     */
    
    // 如果 self.locationView.frame 不包含这个点就直接 return, 不做任何操作
    if(!CGRectContainsPoint(self.locationView.frame, location)) return nil;
    
    // 否则即触摸点在 self.locationView 上，设置 sourceRect，这个 souceRect 就是当你按压售后浮起来的那个矩形区域，即不被虚化的范围。
    CGRect sourceRect = self.locationView.frame;
    [previewingContext setSourceRect:sourceRect];
    
    // 返回预览界面
    TouchViewController *touchVc = [[TouchViewController alloc] init];
    return touchVc;
}
```

```objc
// pop 过程会调用此方法，执行跳转
- (void)previewingContext:(id<UIViewControllerPreviewing>)previewingContext commitViewController:(UIViewController *)viewControllerToCommit {
    
    // Show 要展示的视图
    [self showViewController:viewControllerToCommit sender:self];
}
```

##### 设置预览视图
在接下来的预览视图中添加向上滑显示的菜单，即 TouchViewController 的 .m 文件中自定义你要显示的菜单。
```objc
// 3D Touch 上移显示的视图
-(NSArray<id<UIPreviewActionItem>> *)previewActionItems{
    UIPreviewAction *action1 = [UIPreviewAction actionWithTitle:@"菜单_01" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"click -> 菜单_01");   
    }];
    
    UIPreviewAction *action2 = [UIPreviewAction actionWithTitle:@"菜单_02" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"click -> 菜单_02");
    }];
    
    UIPreviewAction *action3 = [UIPreviewAction actionWithTitle:@"菜单_03" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"click -> 菜单_03");
    }];
    
    // 想要显示多个就定义多个 UIPreviewAction
    NSArray *actions = @[action1,action2,action3];
    return actions;
}
```

##### Tableview 中使用 peek 和 pop
```objc
- (nullable UIViewController *)previewingContext:(id<UIViewControllerPreviewing>)previewingContext viewControllerForLocation:(CGPoint)location {
    // 获取 sourceView
    UIView *sourceView = [previewingContext sourceView];
    
    // 通过坐标点获取 indexPath
    /*
     同样如果sourceView != self.tableView的话，也需要转换坐标系
     point = [self.tableView convertPoint:point fromView:sourceView];
     */
    NSIndexPath*indexPath = [self.tableView indexPathForRowAtPoint:location];
    
    // 如果 indexPath 为 nil，则直接返回 nil
    if(!indexPath) return nil;
    
    // 获得当前cell，设置sourceRect
    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
    CGRect sourceRect = cell.frame;
    
    /*
     这里同时也涉及到坐标系转换
     sourceRect = [self.tableView convertRect:cell.frame toView:sourceView];
     */
    [previewingContext setSourceRect:sourceRect];
    // 返回预览界面
    TouchViewController *touchVc = [[TouchViewController alloc] init];
    return touchVc;
}
```


#### 3D Touch 压力值的运用
直接在需要的 ViewController 的 .m 文件中加入以下方法即可，按压 controller 中的任何视图都会调用这个方法：
```objc
// 按住移动或压力值改变时的回调
-(void)touchesMoved:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    NSArray *arrayTouch = [touches allObjects];
    UITouch *touch = (UITouch *)[arrayTouch lastObject];
    // 注意：如果按压的是 UILabel 或 UIImageView，要将其的 userInteractionEnabled 属性设置为YES
    NSLog(@"move压力 ＝ %f",touch.force);
}
```




