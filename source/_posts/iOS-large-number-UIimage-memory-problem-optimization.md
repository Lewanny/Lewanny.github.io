---
title: iOS 大量图片处理导致占内存过高的优化方法sssssssssss
date: 2018-02-12 20:50:08
tags: 内存优化
categories: Objective-C
---

最近项目中有一个视频剪辑的功能，其中需要把视频的帧图取出来，项目中使用了大量的 `collectionView + imageView`。这就涉及了大量的图片处理，会导致程序的内存占用飙升，如果此时刚好可用内存不大，就会导致程序闪退。

通常报错 `[GatekeeperXPC] Connection to assetsd was interrupted or assetsd died` ，就是内存管理出了问题了，例如使用 `UIImagePickerController` 这个方法来调用图片库和相机的时候，选择的图片是原图，会被加载到内存并占用非常大的内存空间，连续选择多张图片会直接使得程序闪退。这里简单的总结下处理该问题的几种优化方法。


### 检查 UIImage 的加载方式
尽量不要使用 `[UIImage imageNamed:nil];` 这种方式加载，如果缓存中已存在该图片，会从直接从缓存中取得资源，使资源一直停留在内存当中，不会随着对象的销毁而销毁。且相同的图片，图片不会重复加载，内存占用较大。

<!--more-->

如果内存中不存在该图片，会通过传入的文件名对整个工程进行遍历，如果如果找到对应的图片， iOS 系统首先要做的是将这个图片放到系统缓存中去，以备下次使用的时候直接从系统缓存中取，那么试想一下，如果要加载的这个图片的文件量很多，文件大小很大，内存不足，内存泄露，甚至是程序的崩溃都是很容易发生的事。

使用 `imageWithContentsOfFile` 方法代替。
```objc
NSString *imageFile = [NSString stringWithFormat:@"%@/%@.jpg", [[NSBundle mainBundle] resourcePath], fileName];
UIImage* image = [UIImage imageWithContentsOfFile:imageFile];
```

`imageWithContentsOfFile` 仅仅是加载图片，图像数据不会被缓存。因此在加载较大较多图片，以及图片使用情况很少的时候可以使用这两个方法，降低内存消耗，提高程序的稳定性。

综上，当图片文件较小，使用比较频繁的时候那么使用 `imageNamed` 比较好，例如 UITableViewCell、UICollectionCell 等加载同一个图标的时候，这对图像的重复利用是非常有优势的。使用方式加载，图像会被系统以数据的形式加载到程序，当不需要大量重用该图像，或者你需要将图像以数据方式存储到数据库等时，请尽量使用 `imageWithData` 的方式加载图像。



### 对图片进行压缩
#### 使用 UIImageJPEGRepresentation
```objc
NSData *compressData = UIImageJPEGRepresentation(compressImage, 0.5); // 注意 : 0.5 为压缩系数，和压缩后大小无关
```

`UIImageJPEGRepresentation` 函数需要两个参数：`图片的引用`和`压缩系数`。如果对图片的清晰度要求不高，可以通过设置函数的第二个参数，来降低图片数据量。但是压缩效果一般，17MB 的视频通过 0.5 压缩系数的压缩后大小大约 1.8MB 左右（和原图片质量色彩等有关），所以不推荐使用该方法。


#### 使用 UIGraphicsBeginImageContext
选择图片的时候，可根据当前设备的屏幕大小来进行截图，这样得来的图片会由原像素降低为手机屏幕的像素，这样得来的图片大小之后 10-25K 的大小（当然可在其基础上再使用 `UIImageJPEGRepresentation` 进行压缩），画质在不进行缩放的情况下和原图是一样的。推荐使用该方法，压缩效果比较理想，方法如下。

```objc
- (UIImage *)compressImageWith:(UIImage *)image
{
    float imageWidth = image.size.width / screnWidth;
    float imageHeight = image.size.height / screnHeight;
    
    float width = fmax(imageWidth, imageHeight);
    float height = image.size.height/(image.size.width/width);

    float widthScale = imageWidth /width;
    float heightScale = imageHeight /height;

    // 创建一个bitmap的context, 并把它设置成为当前正在使用的context
    UIGraphicsBeginImageContext(CGSizeMake(width, height));

    if (widthScale > heightScale) {
        [image drawInRect:CGRectMake(0, 0, imageWidth /heightScale , height)];
    }
    else {
        [image drawInRect:CGRectMake(0, 0, width , imageHeight /widthScale)];
    }

    // 从当前context中创建一个改变大小后的图片
    UIImage *newImage = UIGraphicsGetImageFromCurrentImageContext();
    // 使当前的context出堆栈
    UIGraphicsEndImageContext();
    return newImage;
}
```


总结：开发中使用到耗费内存的图片加载时，在保证可接受的清晰度范围内。对图片进行适当的压缩处理，优化显示效果，释放内存压力还是有必要的。当然这不只是方法因为是一种思路。我在项目中遇到的问题是从视频中取取大量对应的帧图片，所以实际的解决方法是先对视频的质量进行了压缩处理，也有效的降低了图像处理的压力。


附录：获取 UIimage 大小的方法。
```objc
NSData * imageData = UIImageJPEGRepresentation(image,1);
NSUInteger newLength = [imageData length]/1024;
```
