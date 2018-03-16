---
title: 总结下 iOS 蓝牙开发中常用的数据转换
date: 2017-04-16 19:42:22
tags: 
categories: Objective-C
---

蓝牙开发有一段时间了，处理蓝牙数据的时候，经常遇到进制之间的转换，蓝牙处理的是十六进制（NSData），有时也会把十六进制拆成二进制记录。这里总结下蓝牙开发中常用的进行转换和数据转换。

<!--more-->

#### 十进制转换二进制
```objc
// int 转二进制字符串
- (NSString *)convertIntToBinaryWithInt: (int)intValue
{
    
    int byteBlock = 8,            // 8 bits per byte
    totalBits = (sizeof(int)) * byteBlock,
    binaryDigit = totalBits;
    char ndigit[totalBits + 1];
    while (binaryDigit-- > 0)
    {
        ndigit[binaryDigit] = (intValue & 1) ? '1' : '0';
        intValue >>= 1;
    }
    ndigit[totalBits] = 0;
    NSString *str = [NSString stringWithUTF8String: ndigit];
    return [str substringWithRange: NSMakeRange(str.length - 8, 8)];
}
```

#### 二进制转换十进制
```objc
//  二进制转十进制
- (NSString *)convertBinaryToIntWithBinary:(NSString *)binary
{
    int subBinary = 0 ;
    int  temp = 0 ;
    for (int i = 0; i < binary.length; i ++)
    {
        temp = [[binary substringWithRange:NSMakeRange(i, 1)] intValue];
        temp = temp * powf(2, binary.length - i - 1);
        subBinary += temp;
    }
    NSString * result = [NSString stringWithFormat:@"%d",subBinary];
    return result;
}
```

有点忙，待续....