---
title: HTML+CSS 学习笔记 - CSS 基本样式与布局
date: 2017-02-18 19:47:00
tags: HTML+CSS 
categories: HTML+CSS 
---
使用 CSS 样式的一个好处是通过定义某个样式，可以让不同网页位置的文字有着统一的字体、字号或者颜色等。[本文代码传送](https://github.com/Lewanny/HTML-CSS-Learning/blob/master/%E5%AD%A6%E4%B9%A0%E4%BB%A3%E7%A0%81/HTML-CSS-learn-note-part02-css-style-layout.html)。


### CSS，为网页添加样式
#### 认识 CSS 样式
`CSS` 全称为 `“层叠样式表 (Cascading Style Sheets)”`，它主要是用于定义 HTML 内容在浏览器内的显示样式，如文字大小、颜色、字体加粗等。

<!--more-->

例如如下代码：
```html
p {
  font-size:12px;
  color:red;
  font-weight:blod;
}
```

CSS样式的优势就是只需要一条 CSS 代码即可设置网页样式。


#### CSS 代码语法
CSS 样式由选择符和声明组成，而声明又由属性和值组成，如下所示：

  <img src="http://img.mukewang.com/52fde5c30001b0fe03030117.jpg" width=220>
  
**选择符**：又称为选择器，知名网页中需要样式的元素，其他的元素则不会受到影响
**声明**：在英文的 `{}` 之中的便是声明，属性和值之间用英文的 `:` 隔开。多条声明时，之间用 `;` 隔开。
```html
p {
  font-size:12px; 
  color:red;
 }
```

注意：
最后一条可以不加 `;` ，但是为了方便修改也为了规范，也加上 `;` 。
为了代码更便于阅读，如上，每一条属性各占一行。


#### CSS 代码注释
CSS 中的注释样式为 `/*注释文字*/` ，Html 中使用 `<!--注释文字-->` 来注释。



### CSS 的基本样式
CSS 样式从插入代码的形式分为三种：`内联式`、`嵌入式`和`外部式`。


#### 内联 css 样式，直接写在 HTML 标签内
**内联式**直接把 css 代码写在 HTML 标签中，注意要写在开始标签，而不是结束标签。内联 css 要写在 style=””中，多条样式代码设置可写在一起，中间用 `;` 隔开。如下所示：
```html
<p style="color:red;font-size:12px">红色十二号字体<p>
```

#### 嵌入式 css 样式，写在当前的文件中
嵌入式 css 样式，就是把 css 样式代码写在 标签之间。
嵌入式 css 样式必须写在 之间，并且一般在 之间。它较内联式的好处可以为相同的标签设置同样的样式，是不用为每个标签单独的写样式代码。
```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>嵌入式css样式</title>
<style type="text/css">
	span{font-size:18; color:green}
</style>
</head>
<body>
    <p><span>嵌入式</span>较内联式的好处可以为相同的标签<span>设置同样的样式</span>，是不用为<span>每个标签</span>单独的写样式代码。<p>
</body>
</html>
```


#### 外部式 css 样式，写在单独的文件中
外部式 css 样式(外联式)就是把 css 代码写一个单独的外部文件中，这个 css 样式文件以 “.css” 为扩展名，在 内使用 标签将 css 样式文件链接到 HTML 文件内，如下面代码：
```html
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>嵌入式css样式</title>
<link href="style.css" rel="stylesheet" type="text/css" />
</head>
<>
```

```html
/*style.css 文件中的代码*/
span{
   color:red;
   font-size:20px;
}
```
注意：
1、css 样式文件命名应该有意义，如 `main.css` 。
2、`rel=”stylesheet” type=”text/css”` 是固定写法不可修改。
3、`link` 的位置一般写在标签内。