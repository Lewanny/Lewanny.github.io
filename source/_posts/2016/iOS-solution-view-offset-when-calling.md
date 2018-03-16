---
title: iOS 通话或网络共享等状态栏变化导致 view 偏移的解决方法
date: 2016-11-30 21:06:58
tags:
categories: Objective-C
---

当使用 iPhone 打电话、共享网络或者录音时，会发现状态栏`下移了 20 个像素`，这时对应的 Controller 的 frame 高度也就减少了 20 个像素，如果程序的约束和对应的适配不到位，便会导致某些控价被遮挡显示不全。

所以我们需要根据 view 的 frame 去布局子 view 。

### 原因
经测试，在iPhone6上状态栏下移的时候，self.view.frame 的高度由原来的 667 变成了 647 ， 减少了 20 的高度，还原后仍变回 667 ，这样如果控件的 frame 没有做对应的边距约束，便会发生这样的问题。

<!--more-->

### 解决方案
#### 做好控件的约束
在项目一开始时就要考虑到相应的问题，如在对一个距底部为 10 的 view 布局时，应避免根据屏幕尺寸的绝对布局，应使用 view 距底部 10 这样的约束布局，这样即使状态栏下移，控件距底部仍未 10 不会被遮挡。

#### 监听状态栏变化的通知
经过步骤 1 的处理，若仍有些控件的位置发生位移或重叠，这时我们就可以监听状态栏变化的通知，对view进行重布局。
```objc
// 在init的时候监听状态栏改变的通知 UIApplicationDidChangeStatusBarFrameNotification
[[ NSNotificationCenter defaultCenter ] addObserver : self selector : @selector (layoutControllerSubViews) name : UIApplicationDidChangeStatusBarFrameNotification object : nil ];
```

通过以上方法，便可在通话等状态栏变化的时候对我们应用的控件布局进行相应的调整。
