---
title: iOS 开发笔记之 UISlider
date: 2014-11-09 20:21:34
tags: UISlider
categories: Objective-C
---

#### 改变 UISlider 滑道的高度
```objc
// 控制 slider 的宽和高，这个方法才是真正的改变 slider 滑道的高
- (CGRect)trackRectForBounds:(CGRect)bounds
{
    return CGRectMake(0, 0, CGRectGetWidth(self.frame), _adjustHeight);
}
```

注意：此方法直接调用是无效的，要重写一个继承于 UISlider 的子类，在子类中重写下面的方法才有效。
```objc
-(CGRect)trackRectForBounds:(CGRect)bounds;
```

<!--more-->

#### 改变 UISlider 滑块的大小
可以使用 `setThumbImage` 方法，滑块的大小会随设置的图片尺寸改变。
```objc
[_progressSlider setThumbImage:[UIImage imageNamed:@"ic_slider"] 
                 scaleToSize:CGSizeMake(10, 10) 
                 forState:UIControlStateNormal];
```

改变 UIImage 尺寸的方法，可加在 UIImage 的 Category 方法中。
```objc
/*
 对原来的图片的大小进行处理
 @param image 要处理的图片
 @param size  处理过图片的大小
 */
+ (UIImage *)imageNamed:(NSString *)imagestr scaleToSize:(CGSize)size
{
    UIImage *image = [UIImage imageNamed:imagestr];
    UIGraphicsBeginImageContext(size);
    [image drawInRect:CGRectMake(0,0, size.width, size.height)];
    UIImage *scaleImage=UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return scaleImage;
}
```


#### 增加触控范围
在 UISlider 的拖动手势不灵敏的时候此方法有效。同样需要子类继承 UISlider，然后重写下面的方法。
```objc
- (CGRect)thumbRectForBounds:(CGRect)bounds trackRect:(CGRect)rect value:(float)value
{
	// y 轴方向改变手势范围
   rect.origin.y = rect.origin.y - 10;
   rect.size.height = rect.size.height + 20;
   return CGRectInset([super thumbRectForBounds:bounds trackRect:rect value:value], 10, 10);
}
```


#### 监听 Slider 滑动停止
当监听 Slider 值变化的时候，如果直接在 slider 上绑定事件，则改变一次就需要需要处理一次。这种在某些情况下是不合理的。解决方法如下。
```objc
// 先将 slider 绑定一个事件
[_progressSlider addTarget:self action:@selector(sliderValueChanged:) forControlEvents:UIControlEventValueChanged];
// 将其属性设置成 NO
_progressSlider.continuous = NO;
```

这样就只会在停止滑动的时候发送一个 valuechange 的通知，在 sliderValueChanged 方法中实现你需要进行的操作即可。

持续更新中……