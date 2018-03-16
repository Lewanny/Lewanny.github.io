---
title: iOS 构建 Widget 应用全解（适配 iOS 10）
date: 2016-10-25 21:41:16
tags: Widget 应用
categories: Objective-C
---

iOS 8 出现的 `Application Extension` 极大的方便了用户查看应用的服务等信息。随着 iOS 10 的到来，苹果也越发重视 `Widget` 了，从锁屏页面，下拉通知栏的第一页，还有左滑主页面都可以进入 Widget。通过右上角的“展开”、“折叠”按钮，可以查看 Widget 的全部内容和部分内容。在 iPhone 6s 上面，点击应用 icon 的 3D Touch 界面中，也会有 Widget 的折叠界面的显示。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/WidgetShow.png" width=750></img>

本文接下来要介绍的是如何在你的应用中接入 `Today Extension`，以及 Widget 的添加、UI 的绘制、主应用的吊起、Widget 和主应用数据的共享、针对 iOS 10 的适配等。

<!--more-->

### 在工程中创建（添加）Widget
在想要添加 Widget 的工程中执行以下操作
`Xcode 菜单 -> File -> New -> Target… -> 选择 Today Extension`，如下所示。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/WidgetCreate.png" width=600></img>

创建完之后多出 Widget 的文件夹及内容，名字默认为 TodayViewController 可自行修改。至此，为工程添加 Widget 就算成功了，运行工程就可以看到 Widget 的效果了。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/WidgetFolder.png" width=400></img>
  
  
  
### Widget 的使用
#### 绘制 UI
使用 `Interface Builder`。
工程默认的方式就是使用 Interface Builder，如果实现简单的布局的话可以考虑这种方式。

使用代码进行布局。
使用代码需要修改一下 widget 下的 plist 文件。首先将原有 `NSExtensionMainStoryboard` 字段删除，添加字段 `NSExtensionPrincipalClass` ，value 是你所写的 controller 的名称，一般默认的都是 TodayViewController。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/Plist.png" width=800></img>
  
  
#### 设置 Widget 的 size
iOS 10 之后，Widget 支持`展开及折叠`两种展现方式，通过设置widgetLargestAvailableDisplayMode 属性可以让 Widget 程序实现展开布局。
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    if ([UIDevice currentDevice].systemVersion.doubleValue >= 10.0)
    {
        // iOS 10 以后
        self.extensionContext.widgetLargestAvailableDisplayMode = NCWidgetDisplayModeExpanded;
    }
    self.preferredContentSize = CGSizeMake(kWidgetWidth, 110);
}
```

重写切换展开及折叠布局时的方法（iOS 10 以后）。
```objc
- (void)widgetActiveDisplayModeDidChange:(NCWidgetDisplayMode)activeDisplayMode withMaximumSize:(CGSize)maxSize
{
    NSLog(@"maxWidth %f maxHeight %f",maxSize.width,maxSize.height);
    if (activeDisplayMode == NCWidgetDisplayModeCompact)
    {
        self.preferredContentSize = CGSizeMake(maxSize.width, 110);
    }
    else
    {
        self.preferredContentSize = CGSizeMake(maxSize.width, 200);
    }
}
```

iOS 10 之前绘制的内容与左侧边界有一定距离(约30px)。可用以下方法解决（iOS 10）不存在此问题。
```objc
- (UIEdgeInsets)widgetMarginInsetsForProposedMarginInsets:(UIEdgeInsets)defaultMarginInsets
{
    return UIEdgeInsetsMake(0, 10, 0, 10);
}
```


#### 唤起应用
因为 extension 和 containing app 是两个`完全独立的进程`，所以它们之间不能直接通信(不能像应用内部点击按钮，跳转到指定页面)。为了实现 widget 调起 app，这里通过 openURL 的方式来启动 containing app。

为你的应用设置它的 URL Scheme。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/setUrltype.png" width=800></img>
  
  使用以下代码来打开主应用，其中 `appextension://xxx` 为你的应用的 URL Scheme，如果想要快速启动微信换成 `wechat://` 即可，其他的应用全部类似。
```objc
- (void)openURLContainingAPP{
     //通过 extensionContext 借助 host app 调起 app
     [self.extensionContext openURL:[NSURL URLWithString:@"appextension://xxx"] completionHandler:^(BOOL success) { 
        NSLog(@"open url result:%d",success); 
    }];
}
```

相应的，在 AppDelegate 中实现以下方法，这里可以处理传过来的 action，对于传过来不同的值可以进行不同的操作。
```objc
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    if ([[url absoluteString] hasPrefix:@"appextension"])
    {
        // 处理你的事情
    }
    return  YES;
}
```


#### 数据的共享
扩展程序一般都不是脱离宿主程序单独运行的，难免需要和宿主程序进行数据交互。而相对于一般的 App，数据可以用单例，NSUserDefault 等等。但由于拓展与宿主应用是两个完全独立的 App，并且iOS应用基于沙盒的形式限制，所以一般的共享数据方法都是实现不了数据共享，这里就需要使用 `App Groups`。

widget 也是需要`单独申请 App id` 的，需要配置证书和 Provisioning Profiles文件，到你的开发者中心后台分别添加宿主程序和 widget 程序的 App id，widget 程序的 Bundle id 需以主程序的 Bundle id 为前缀，例如 com.xxx 和 com.xxx.widget 。添加完成后需要在后台创建 App Groups 并包含两个 Bundle id 。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/AppGroud.png" width=750></img>
  
打开应用的 Capabilities， 找到 App Groups 选项，勾选建立好的 group ，Steps 全部没问题即可。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/AppGroupOk.png" width=650></img>
  
  
#### 两种数据存储方式
- **使用 NSUserDefault**
这里不能使用 `[NSUserDefaults standardUserDefaults];` 方法来初始化 NSUserDefault 对象，正像之前所说，由于沙盒机制，拓展应用是不允许访问宿主应用的沙盒路径的，因此上述用法是不对的，需要搭配 `app group` 来实例化 UserDefaults 。
```objc
// 写入数据
NSUserDefaults *userDefaults = [[NSUserDefaults alloc] initWithSuiteName:@"group.com.snaillove.app.tool.quickpay"];
[userDefaults setObject:self.textField.text forKey:@"widget"];
[userDefaults synchronize];
// 读取数据
NSUserDefaults *userDefaults = [[NSUserDefaults alloc] initWithSuiteName:@"group.com.snaillove.app.tool.quickpay"];
self.contentStr = [userDefaults objectForKey:@"widget"];
```

- **使用 NSFileManager 共享数据**
写入数据 和 读取数据。
```objc
// 写入数据
-(BOOL)saveDataByNSFileManager
{
  NSError *err = nil;
  NSURL *containerURL = [[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:@"group.com.xxx"];
  containerURL = [containerURL URLByAppendingPathComponent:@"Library/Caches/ widget"];
  NSString *value = @"asdfasdfasf";
  BOOL result = [value writeToURL:containerURL atomically:YES encoding:NSUTF8StringEncoding error:&err];
  if (!result)
  {
      NSLog(@"%@",err);
  }
  else
  {
      NSLog(@"save value:%@ success.",value);
  }
  return result;
}
// 读取数据
-(NSString *)readDataByNSFileManager
{
  NSError *err = nil;
  NSURL *containerURL = [[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:@"group.com.xxx"];
  containerURL = [containerURL URLByAppendingPathComponent:@"Library/Caches/ widget"];
  NSString *value = [NSString stringWithContentsOfURL:containerURL encoding: NSUTF8StringEncoding error:&err];
  return value;
}
```


### 注意事项
Widget 因为有自己的 `App id`，所以也需要一套自己的配置文件(不同于 host 应用的)。
如果发现 widget 应用没有 AppIcon ，则需要在 AppIcon 的 Target Memberhip 选项中勾选 widget 应用。

  <img src="http://omkug1guu.bkt.clouddn.com/iOS-build-widget-app-complete-solution/icon.png" width=820></img>
  
同样默认 widget 应用是无法使用宿主应用的类和资源文件的，如果想要使用的话同上面 AppIcon 方法一样，Target Memberhip 选项中勾选 widget 应用即可。

到此，iOS 构建 Widget 就基本 ok 了。待续……



