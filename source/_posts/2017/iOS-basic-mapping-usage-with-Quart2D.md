---
title: iOS 使用 Quart2D 绘图基础
date: 2017-05-15 21:32:50
tags:
categories: Objective-C
---
Quartz 2D 是⼀个二维绘图引擎，同时支持 iOS 和 Mac 平台。 Quartz 2D 能够绘制图形、绘制文字、绘制\生成图片、读取\生成PDF、截图\裁剪图片等。其实，iOS 中⼤部分控件的内容都是通过 Quartz2D 画出来的，因此Quartz2D 在 iOS 开发中很重要的⼀个价值就是自定义 UI 控件)。

在 Mac OS X 中，Quartz 2D 可以与其它图形图像技术混合使用，如 Core Image、Core Video、OpenGL、QuickTime。例如，通过使用  QuickTime 的 GraphicsImportCreateCGImage 函数，可以用 Quartz 从一个 QuickTime 图形导入器中创建一个图像。

<!--more-->

### 基础准备
#### 简介
Quartz2D 的 API 是纯 C 语⾔的，它的 API 来自于 Core Graphics 框架。数据类型和函数基本都以 CG 作为前缀，例如 CGContextRef、CGPathRef、CGContextStrokePath() 等。


#### 图形上下文
图形上下文 (Graphics Context) 是一个 CGContextRef 类型的数据。图形上下文可以保存绘图信息、绘图状态、决定绘制的输出目标（PDF 文件、Bitmap 窗口等）。


#### drawRect 方法
```objc
-[viewController loadView]
-[viewController viewDidLoad]
-[viewController viewWillAppear:]
-[viewController drawRect:]
-[viewController viewDidAppear:]
```
在 `drawRect:` 方法中取得上下文后，就可以绘制东西到 view 上，view 内部有个 layer(图层) 属性，drawRect: 方法中取得的是 Layer Graphics Context ，因此绘制的东西其实是绘制到 layer 上面。view 之所以能显示东西，也完全是因为它内部的 layer 。


#### 绘制步骤
1. 创建一个 UIView 的子类，重写这个类的 `- (void)drawRect:(CGRect)rect` 方法。
2. `UIGraphicsGetCurrentContext()` 获取当前的上下文（这里只能获取一次，并且只能在 drawRect 方法中获取）
3. 描述路径、形状（就是处理想要显示的样子）
4. 把描述好的路径、形状添加早上下文中 `CGContextAddPath()`。
5. 显示上下文内容。

 
### 画线
两点确定一条直线，先确定线的起点，然后确定终点，绘制出路径就 ok 了。
```objc
#pragma mark - 画线
- (void)drawRect:(CGRect)rect {
    NSLog(@"%s",__func__);

    // 1.获取上下文
    CGContextRef contextRef = UIGraphicsGetCurrentContext();

    // 2.路径描述
    UIBezierPath *linePath = [UIBezierPath bezierPath];
    [linePath moveToPoint:CGPointMake(0, 0)]; // 起点
    [linePath addLineToPoint:CGPointMake(WIDTH, HEIGHT)]; // 终点
    [[UIColor blackColor] setStroke]; // 设置颜色
    CGContextSetLineWidth(contextRef, 5); // 设置线宽

    // 3.添加路径
    CGContextAddPath(contextRef, linePath.CGPath);

    // 4.显示路径
    CGContextStrokePath(contextRef);
}
```
<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Line.png" width=400></img>


### 画矩形
绘制矩形，却要确定它的四个顶点和四条线，方法一。
```objc
#pragma mark - 画矩形
- (void)drawRect:(CGRect)rect {
    // 1.获取上下文
    CGContextRef contextRef = UIGraphicsGetCurrentContext();

    // 2.描述路径
    UIBezierPath *rectPath = [UIBezierPath bezierPath];
    [rectPath moveToPoint:CGPointMake(30, 30)]; // 第一个点
    [rectPath addLineToPoint:CGPointMake(WIDTH-60, 30)]; // 第二个点
    [rectPath addLineToPoint:CGPointMake(WIDTH-60, HEIGHT-60)]; // 第三个点
    [rectPath addLineToPoint:CGPointMake(30, HEIGHT-60)]; // 第四个点
    [rectPath closePath]; // 闭合路径，同 [rectPath addLineToPoint:CGPointMake(30, 30)];
    [[UIColor blackColor] setStroke]; // 设置颜色

    // 3.添加路径
    CGContextAddPath(contextRef, rectPath.CGPath);

    // 4.显示路径
    CGContextStrokePath(contextRef);
}
```

方法二。
```objc
- (void)drawRect:(CGRect)rect {
    // 1.获取上下文
    CGContextRef contextRef = UIGraphicsGetCurrentContext();

    // 2.描述路径
    UIBezierPath *rectPath = [UIBezierPath bezierPathWithRect:CGRectMake(30, 30, WIDTH-60, HEIGHT-60)]; // 路径
    [[UIColor blackColor] setFill]; // 颜色

    // 3.添加、显示路径
    CGContextAddPath(contextRef, rectPath.CGPath);
    CGContextFillPath(contextRef);
}
```

<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Rect.png" width=400></img>


```objc
[[UIColor greenColor] setStroke]; // 设置描边颜色
CGContextStrokePath(contextRef); // 显示描边路径
[[UIColor greenColor] set]; // 设置填充颜色
CGContextFillPath(contextRef); // 显示填充路径
```

### 画圆
第一种画圆思路需要确定圆形的圆心点、半径以及旋转角度。
```objc
#pragma mark - 画圆
- (void)drawRect:(CGRect)rect {
    // 1、获取当前上下文
    CGContextRef contextRef = UIGraphicsGetCurrentContext();

    // 2. 描述路径 -> ArcCenter:中心点 / radius:半径 / startAngle：起始角度 / endAngle：结束角度 / clockwise：是否逆时针
    UIBezierPath *circlePath = [UIBezierPath bezierPathWithArcCenter:CGPointMake(WIDTH/2, HEIGHT/2) radius:120 startAngle:0 endAngle:M_PI*2 clockwise:YES];
    [[UIColor blackColor] setFill]; //颜色

    // 3. 填充显示上下文
    CGContextAddPath(contextRef, circlePath.CGPath);
    CGContextFillPath(contextRef); // 空心圆使用 CGContextStrokePath
}
```

第二种方法利用画椭圆的方法，圆形是特殊的椭圆形。
```objc
- (void)drawRect:(CGRect)rect {
    // 1、获取当前上下文
    CGContextRef contextRef =UIGraphicsGetCurrentContext();

    //2. 描述路径，画椭圆的方法
    UIBezierPath *ovalePath = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(30, 30, 200, 200)];

    // 3. 填充显示上下文
    CGContextAddPath(contextRef, ovalePath.CGPath);
    CGContextStrokePath(contextRef);
}
```
<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Circle.png" width=400></img>


### 扇形
扇形是圆形的一部分。
```objc
// 计算度转弧度
static inline float radians(double degrees) {
    return degrees * M_PI / 180;
}

static inline void drawArc(CGContextRef ctx, CGPoint point, float angle_start, float angle_end, UIColor* color,float radius) {
    CGContextMoveToPoint(ctx, point.x, point.y);
    CGContextSetFillColor(ctx, CGColorGetComponents( [color CGColor]));
    CGContextAddArc(ctx, point.x, point.y, radius,  angle_start, angle_end, 0);
    //CGContextClosePath(ctx);
    CGContextFillPath(ctx);
}

-(void)drawRect:(CGRect)rect {
    CGPoint cent = CGPointMake(WIDTH/2, HEIGHT/2);
    CGContextRef ctx = UIGraphicsGetCurrentContext();
    CGContextClearRect(ctx, rect);

    float angle_start = radians(0.0);
    float angle_end = radians(121.0);
    drawArc(ctx, cent, angle_start, angle_end, [UIColor blueColor], _radius);

    angle_start = angle_end;
    angle_end = radians(228.0);
    drawArc(ctx, cent, angle_start, angle_end, [UIColor greenColor], _radius);

    angle_start = angle_end;
    angle_end = radians(260);
    drawArc(ctx, cent, angle_start, angle_end, [UIColor orangeColor], _radius);

    angle_start = angle_end;
    angle_end = radians(360);
    drawArc(ctx, cent, angle_start, angle_end, [UIColor purpleColor], _radius);
}
```
<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Sector.png" width=400></img>

如果需要更新角度或者颜色等，需要在更新的地方调用 `[self setNeedsDisplay];` 即可。


### 画文字
简单绘制。
```objc
- (void)drawRect:(CGRect)rect {
    CGContextRef contextRef =UIGraphicsGetCurrentContext();

    // 绘制文字，默认从 (0,0) 开始
    NSString *norText = @"我是普通文字";
    [norText drawInRect:rect withAttributes:nil];

    CGContextStrokePath(contextRef);
}
```

其他的设置。
```objc
- (void)drawRect:(CGRect)rect {
    CGContextRef contextRef = UIGraphicsGetCurrentContext();

    // 字体设置
    NSString *text = @"艺术字体";
    NSMutableDictionary * dict = [NSMutableDictionary dictionary];
    dict[NSFontAttributeName] = [UIFont systemFontOfSize:32]; // 字体大小
    dict[NSForegroundColorAttributeName] = [UIColor blueColor]; // 字体前景色
    dict[NSBackgroundColorAttributeName] = [UIColor blackColor]; // 字体背景色

    //字体阴影
    NSShadow * shadow = [[NSShadow alloc] init];
    shadow.shadowOffset = CGSizeMake(2, 2); // 阴影偏移量
    shadow.shadowColor = [UIColor orangeColor]; // 阴影颜色
    shadow.shadowBlurRadius = 5; // 高斯模糊
    dict[NSShadowAttributeName] = shadow;

    //字体间距
    dict[NSKernAttributeName] = @10;

    // 从某一点开始绘制
    [text drawAtPoint:CGPointMake(80, 100) withAttributes:dict];
    CGContextStrokePath(contextRef);
}
```
<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Words.png" width=400></img>


### 画图片
```objc
#pragma mark - 画图片
- (void)drawRect:(CGRect)rect {
    CGContextRef contextRef = UIGraphicsGetCurrentContext();
    UIImage *image = [UIImage imageNamed:@"avatar"];
    
    // 直接绘制
//    [image drawInRect:rect];

    // 从某个点开始绘制
//    [image drawAtPoint:CGPointMake(60, 60)];

    //从某一点开始绘制图片，并且设置混合模式以及透明度
//    [image drawAtPoint:CGPointMake(0, 0) blendMode:kCGBlendModeNormal alpha:1];

    // 绘制图片的大小，并且设置混合模式以及透明度
    [image drawInRect:CGRectMake(30, 30, WIDTH-60, HEIGHT-60) blendMode:kCGBlendModeNormal alpha:1];

    CGContextFillPath(contextRef);
}
```


### 进度条
```objc
- (void)drawRect:(CGRect)rect {
    CGFloat startA = -M_PI_2;
    CGFloat endA   = -M_PI_2 + self.progress.value* M_PI*2;
    UIBezierPath * path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(self.bounds.size.width*0.5, self.bounds.size.height*0.5) radius:self.bounds.size.width*0.3-10 startAngle:startA endAngle:endA clockwise:YES];
    [path moveToPoint:CGPointMake(30, 30)];
    [[UIColor colorWithRed:0 green:(1-self.progress.value) blue:self.progress.value alpha:1]setStroke];
    path.lineWidth = 5.0f;
    [path stroke];
}
```
<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Slider.png" width=400></img>



### 如何绘制三条不同颜色的线
方法一。
```objc
- (void)drawRect:(CGRect)rect {
    //1 获取上下文
    //分别设置线段的颜色
    CGContextRef purple = UIGraphicsGetCurrentContext();
    [[UIColor purpleColor]setStroke];
    CGContextSaveGState(purple);

    CGContextRef orange = UIGraphicsGetCurrentContext();
    [[UIColor orangeColor]setStroke];
    CGContextSaveGState(orange);

    CGContextRef green = UIGraphicsGetCurrentContext();
    [[UIColor greenColor]setStroke];
    CGContextSaveGState(green);


    UIBezierPath * path = [UIBezierPath bezierPath];
    //设置线宽
    path.lineWidth = 5;

    //把紫色的上下文从栈中取出来
    CGContextRestoreGState(purple);
    //第一条线
    [[UIColor purpleColor]setStroke];
    [path moveToPoint:CGPointMake(10, 10)];
    [path addLineToPoint:CGPointMake(10, 100)];
    [path stroke];


    //把紫色的上下文从栈中取出来
    CGContextRestoreGState(orange);
    path = [UIBezierPath bezierPath];
    //设置线宽
    path.lineWidth = 9;
    //第二条线
    [[UIColor orangeColor]setStroke];
    [path moveToPoint:CGPointMake(30, 10)];
    [path addLineToPoint:CGPointMake(30, 100)];
    [path stroke];

    //把紫色的上下文从栈中取出来
    CGContextRestoreGState(green);
    path = [UIBezierPath bezierPath];
    //设置线宽
    path.lineWidth = 3;
    //第三条线
    [[UIColor greenColor]setStroke];
    [path moveToPoint:CGPointMake(50, 10)];
    [path addLineToPoint:CGPointMake(50, 100)];
    [path stroke];
}
```

方法二。
```objc
- (void)drawRect:(CGRect)rect {
    [[self bezierPathWithPoint:CGPointMake(10, 10) endPoint:CGPointMake(10, 180) lineColor:[UIColor purpleColor] lineWidth:6] stroke];
    [[self bezierPathWithPoint:CGPointMake(50, 10) endPoint:CGPointMake(50, 180) lineColor:[UIColor greenColor] lineWidth:6] stroke];
    [[self bezierPathWithPoint:CGPointMake(90, 10) endPoint:CGPointMake(90, 180) lineColor:[UIColor orangeColor] lineWidth:6] stroke];
}

- (UIBezierPath *)bezierPathWithPoint:(CGPoint)startPoint endPoint:(CGPoint) endPoint lineColor:(UIColor*)lineColor lineWidth:(CGFloat)lineWidth{
    UIBezierPath * path = [UIBezierPath bezierPath];
    [lineColor setStroke];
    path.lineWidth = lineWidth;
    [path moveToPoint:startPoint];
    [path addLineToPoint:endPoint];
    return path;
}
```

<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_ThreeLines.png" width=400></img>



### 渐变色
方法一。
```objc
- (void)drawRect:(CGRect)rect {
    CAGradientLayer * layer = [CAGradientLayer layer];
    // 设置大小
    layer.frame = CGRectMake(0, 400, 300, 3);
    // 设置颜色
    layer.colors = @[(__bridge id)[UIColor colorWithRed:204.0 / 255.0 green:224.0 / 255.0 blue:244.0 / 255.0 alpha:1].CGColor,
                     (__bridge id)[UIColor colorWithRed:29.0 / 255.0 green:156.0 / 255.0 blue:215.0 / 255.0 alpha:1].CGColor,
                     (__bridge id)[UIColor colorWithRed:255.0 / 255.0 green:50.0 / 255.0 blue:126.0 / 255.0 alpha:1].CGColor];
    // 渐变层的相对位置,起始点为0,终止点为1,中间点为 (point-startpoint)/(endpoint-startpoint)
    layer.locations = @[@0,@.5,@1];
    // 渐变方向
    layer.startPoint = CGPointMake(0, 1);
    layer.endPoint = CGPointMake(1, 1);
    [self.layer addSublayer:layer];
}
```

方法二。
```objc
- (void)drawRect:(CGRect)rect {
    [self drawLine];
    [self drawLineWithStartColor:[UIColor redColor] endColor:[UIColor greenColor]];
}

- (void)drawLineWithStartColor:(UIColor *)startColor endColor:(UIColor *)endColor{

    CGContextRef ctx = UIGraphicsGetCurrentContext();

    // 创建颜色空间
    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
    CGFloat locations[] = { 0.0, 1.0 };
    // 创建颜色数组，NSArray 只能存储 OC 对象，所以需要桥接
    NSArray *colors = @[(__bridge id)startColor.CGColor,(__bridge id)endColor.CGColor];

    CGGradientRef gradient = CGGradientCreateWithColors(colorSpace, (__bridge CFArrayRef) colors, locations);
    //释放
    CFRelease(colorSpace);

    // 保存一份当前上下文，压栈
    CGContextSaveGState(ctx);

    CGFloat width = self.frame.size.width;
    CGContextMoveToPoint(ctx, 0, 120);
    CGContextAddLineToPoint(ctx, width, 120);
    CGContextAddLineToPoint(ctx, width, 123);
    CGContextAddLineToPoint(ctx, 0, 123);
    //裁剪
    CGContextEOClip(ctx);

    //绘制渐变
    CGContextDrawLinearGradient(ctx, gradient, CGPointMake(0, 100), CGPointMake(self.frame.size.width, 100), kCGGradientDrawsBeforeStartLocation);

    //恢复一份当前的上下文，出栈
    CGContextRestoreGState(ctx);
    CGColorSpaceRelease(colorSpace);
    CGGradientRelease(gradient);
}

- (void)drawLine{
    // 获取上下文
    CGContextRef ctx = UIGraphicsGetCurrentContext();
    
    CGContextMoveToPoint(ctx, 0, 100);
    CGContextAddLineToPoint(ctx, 300, 100);
    CGContextSetLineWidth(ctx, 3);

    // 填充路径
    CGContextStrokePath(ctx);
    // 释放
    CGContextRelease(ctx);
}
```

<img src="http://omkug1guu.bkt.clouddn.com/iOS-basic-mapping-usage-with-Quart2D/Quart2D_Gradient.png" width=400></img>
