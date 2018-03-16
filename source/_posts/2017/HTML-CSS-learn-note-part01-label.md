---
title: HTML+CSS 学习笔记 - 标签部分
date: 2017-02-09 21:29:07
tags: HTML+CSS 
categories: HTML+CSS 
---

学习 web 前端开发技术需要掌握：HTML、CSS 和 JavaScript 语言。其中，HTML 是网页内容的载体，就是页面上的信息，包括文字、图片、视频等。CSS 样式是表现，如标题颜色、背景颜色、背景图、边框等。JavaScript 用来实现网页上的特效效果，如鼠标滑过的下拉菜单和动画等交互都是用 JavaScript 实现的。

IDE 推荐 Adobe 前端神器 [Brackets](http://brackets.io/) ，简单易用。

下面是学习 HTML+CSS 的学习笔记。本文的[源码地址传送门](https://github.com/Lewanny/HTML-CSS-Learning/blob/master/%E5%AD%A6%E4%B9%A0%E4%BB%A3%E7%A0%81/learning-note-part01-label.html)。

<!--more-->


### 标签简介
#### 什么是标签
1. `标签`由英文尖括号 `< 和 >` 括起来，如 `<html>` 就是一个标签。

2. `html` 中的标签一般都是成对出现的，分开始标签和结束标签。结束标签比开始标签多了一个 `/` 。

3. 标签之间是可以嵌套的，如：
```html
<div><p> </p></div>
```

4. HTML 标签不区分大小写，如 `<h1>` 和 `<H1>` 是一样的，以`小写`为准。

可以说我们平时看到的各种各样的网页都是由 `HTML 标签` 组成的。如下面的这个例子。
```html
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>认识html标签</title>
    </head>
    <body>
        <h2>勇气哈哈</h2>
        <p>三年级时，我还是一个胆小如鼠的小女孩，上课从来不敢回答老师提出的问题，生怕回答错了老师会批评我。就一直没有这个勇气来回答老师提出的问题。学校举办的活动我也没勇气参加。</p>
        <p>到了三年级下学期时，我们班上了一节公开课，老师提出了一个很简单的问题，班里很多同学都举手了，甚至成绩比我差很多的，也举手了，还说着："我来，我来。"我环顾了四周，就我没有举手。</p>
        <img src="http://img.mukewang.com/52b4113500018cf102000200.jpg" >
    </body>
</html>
```


#### html 文件的结构
一个HTML文件是有自己固定的结构的。
```html
<html>
  <head>...</head>
  <body>...</body>
</html>
```

1. <html></html> 称为`根标签`，所有的网页标签都在根标签中。

2. <head> 标签用于定义文档的头部，它是所有头部元素的`容器`。头部元素有 `<title>、<script>、 <style>、<link>、 <meta>` 等标签，头部标签在下一小节中会有详细介绍。

3. 在 <body> 和 </body> 标签之间的内容是网页的`主要内容`，如 `<h1>、<p>、<a>、<img>` 等网页内容标签，在这里的标签中的内容会在浏览器中显示出来。


#### 注释
```html
<!--注释文字 -->
```

html 的注释为 `<!–我是注释–!>`

CSS的注释为 `/我是注释/`

JS的单行注释为 `//我是注释`，多行注释为 `/我是注释/`



### 标签第一部分
#### body 标签
网页上显示的内容都放在 `<body>` 标签中。

#### 标题标签
```html
<hx>标题文本</hx>
如一级标题：<h1>标题内容</h1>
```
除了 `<h1>` 标签外，还有 `<h2>、<h3>、<h4>、<h5>、<h6>`，数字越大字号越小，h1 字号最大，h6 最小。


#### 段落标签
```html
<p>段落内容</p>
```
多个段落分别放在多个 `<p>` 标签中。


#### 强调标签
```html
<em>需要强调的文本</em>
<strong>需要强调的文本</strong>
```
`<em>` 标签表示强调，默认用`斜体`标示。`<strong>` 表示更加强调，默认用`粗体`表示。两者均可用 CSS 样式来修改。


#### span 标签
`<span>` 标签是没有语义的，他的作用是设置单独的样式。
```html
<span>文字</span>
下面例子将 "美国梦" 改为了蓝色
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>了不起的盖茨比</title>
<style>
span{
    color:blue;
}
</style>
</head>
<body>
    <p>1922年的春天，一个想要成名名叫尼克•卡拉威（托比•马奎尔Tobey Maguire 饰）的作家，离开了美国中西部，来到了纽约。那是一个道德感渐失，爵士乐流行，走私为王，股票飞涨的时代。为了追寻他的<span>美国梦</span>，他搬入纽约附近一海湾居住。</p>
    <p>菲茨杰拉德，二十世纪美国文学巨擘之一，兼具作家和编剧双重身份。他以诗人的敏感和戏剧家的想象为"爵士乐时代"吟唱华丽挽歌，其诗人和梦想家的气质亦为那个奢靡年代的不二注解。</p>
</body>
</html>
```


#### 短文本引用
引用别人的话使用 `<q>` 标签，引用的话默认会被加上双引号，注意这里双引号不是重点，而在于引用别人的话。
```html
最初知道庄子，是从<q>庄生晓梦迷蝴蝶，望帝春心托杜鹃</q>开始的。
```

`<blockquote>` 用来引用长文本，不同于 <q> 标签的是浏览器对 `<blockquote>` 标签解析的是左右缩进样式。

```html
<blockquote>明月出天山，苍茫云海间。长风几万里，吹度玉门关。汉下白登道，胡窥青海湾。由来征战地，不见有人还。 戍客望边色，思归多苦颜。高楼当此夜，叹息未应闲。</blockquote>
```


#### 分行显示文本
html 中是会自动忽略空格和回车的，在需要加回车的地方加入 `<br />` 标签。相当于 word 文档中的空格。
```html
<h2>《咏佳》</h2>
<p>
暗淡轻黄体性柔，<br />
情疏意远只留香。<br />
何须浅碧深红色，<br />
自是花中第一流。
</p>
```

与之前的标签不一样，`<br />` 是一个空标签，空标签只需写一个开始标签。这样的标签有 `<br />`、`<hr />`、和 `<img />` 。


#### 添加空格
空格和回车在html中是会被自动忽略的，想要输入空格，必须写 `&nbsp;` 。
```html
暗淡&nbsp;&nbsp;&nbsp;&nbsp;轻黄体性柔，情疏迹远只留香。
```

注意 `&nbsp;` 是带有 `;` 的。


#### 添加水平线
使用 `<hr>` 标签，html1.0 中为 `<hr />`，html4.01 中为 `<hr>`，现在一般使用 hxml1.0 版本的规范。
```html
<hr />
```

`<hr />` 和 `<br />` 一样，也是一个`空标签`，所以也是有开始没有结束，当然其样式也可用 CSS 来修改。


#### 网页加入地址信息
使用 `<adress>` 标签为网页加入地址信息。如公司的地址邮件等信息。默认用斜体显示。
```html
<address>深圳市南山区南光路。</address>
```



#### 插入代码
文章中插入单行代码可以使用 `<code>` 标签。多行时可以使用 `<pre>` 标签。
```html
<code>var i = 30; i++;</code>
<pre>
var message="欢迎";
for(var i=1;i<=10;i++)<br>
{<br>
    alert(message); <br>
}<br>
</pre>
```


### 标签第二部分
#### 添加新闻信息列表(无序)
网页中会有很多的列表，如新闻列表、图片列表等。这样的列表可以使用 `ul-li` 标签来完成，其代表没有前后顺序的信息列表。
```html
<ul>
	<li>少年</li>
	<li>你好么</li>
	<li>美丽的出现</li>
</ul>
```


#### 添加有序的信息列表
有序列表使用标签 `<ol-li>` 来实现。
```html
<ol>
	<li>貂蝉</li>
	<li>西施</li>	
	<li>昭君</li>
</ol>
```


#### div 在排版中的作用
网页制作中了，通常把一些独立的逻辑划分出来，放在一个 `<div>` 标签中，这个标签就相当于一个容器。
```html
<!DOCTYPE HTML>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>div标签</title>
</head>
<body>
    <div>
    <h2>热门课程排行榜</h2>
    <ol>
        <li>前端开发面试心法 </li>
        <li>零基础学习html</li>
        <li>javascript全攻略</li>
    </ol>
    </div>
    
    <div>
    <h2>最新课程排行</h2>
    <ol>
        <li>版本管理工具介绍—Git篇 </li>
        <li>Canvas绘图详解</li>
        <li>QQ5.0侧滑菜单</li>
    </ol>
    </div>
</body>
</html>
```


#### 为 div 命名，使逻辑更加清晰
可以用 `id` 属性为每个 div 命名，是逻辑更加清晰，每个名称是唯一的。
```html
<div id="名称">...</div>
<div id="hotList">
   <h2>热门课程排行榜</h2>
    <ol>
       <li>前端开发面试心法 </li>
      <li>零基础学习html</li>
       <li>javascript全攻略</li>
    </ol>
</div>
<div id="learningGuide">
    <h2>web前端开发导学课程</h2>
    <ul>
       <li>网页专业名词大扫盲 </li>
       <li>网站职位定位指南</li>
       <li>为您解密Yahoo网站制作流程</li>
    </ul>
</div>
```


#### 网页上的表格，table 标签
想要创建表格的四个元素：`table、tbody、tr、th、td`

1. 整个表格以 `<table>` 标记开始，`</table>` 标记结束。
2. `<tbodu>...</tbody>` 当表格中内容很多时，表格会边下载边显示，但是加上 `<tbody>` 标签后，就会等下载完成才显示。
3. `<tr>...</tr>` 表格的一行，表格有多少行，就有多少对 tr。
4. `<tb>...</tb>`，表格的一个单元格，一行包含几对 tb 说明表格就有几列。
5. `<th>...</th>` 表格头部的单元格，表格的表头。

表格中列的个数，取决于一行中单元格的个数。
```html
<table>
  <tbody>
    <tr>
      <th>班级</th>
      <th>学生数</th>
      <th>平均成绩</th>
    </tr>
    <tr>
      <td>一班</td>
      <td>30</td>
      <td>89</td>
    </tr>
    <tr>
      <td>二班</td>
      <td>35</td>
      <td>85</td>
    </tr>
    <tr>
       <td>三班</td>
       <td>38</td>
       <td>88</td>
    </tr>
  </tbody>
</table>
```


#### 用 CSS 样式，为表格加入边框。
下面使用了 css 样式代码为 `th`、`td` 单元格加粗细为一个像素的黑色边框。
```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>为表格添加边框</title>
<style type="text/css">
table tr td,th{
    border:1px solid #000;
}
</style>
</head>
<body>
<table summary="">
  <tr>
    <th>班级</th>
    <th>学生数</th>
    <th>平均成绩</th>
  </tr>
  <tr>
    <td>一班</td>
    <td>30</td>
    <td>89</td>
  </tr>
  <tr>
    <td>二班</td>
    <td>35</td>
    <td>85</td>
  </tr>
  <tr>
    <td>三班</td>
    <td>32</td>
    <td>80</td>
  </tr>
</table>
</body>
</html>
```


#### 为表格添加标题和摘要
摘要是不会显示出来的，只是为了增加表格的可读性（语义化），也可帮助搜索引擎更好地读懂其内容。
```html
<table summary="表格简介摘要">
```

标题用来描述表格内容，显示在表格上方，使用 `<caption>...</caption>` 标签。
```html
<table>
   <caption>表格标题</caption>
	<tr>
	   <td>...</td>
	   <td>...</rd>
		...
	</tr>
</table>
```



### 标签第三部分
#### 超链接的实现
使用 `<a>` 标签可以实现超链接。有链接的地方就会有这个标
```html
<a href="目标地址" title="鼠标滑过显示的文本">连接显示的文本</a>
<a href="www.baidu.com" title="点击去度娘搜索">点我搜索</a>
```

`title` 的作用在网站开发中的作用很大，主要方便搜索引擎了解链接地址的内容（语义化更友好）。链接默认显示为蓝色字体，后面可以通过 CSS 样式修改。

打开的网页默认在当前的页面打开，如果需要在新的页面打开，需要使用 `target` 。
```html
<a href="www.baidu.com target="_blank">click me</a>
```


#### 网页中链接 E-mail 地址
`<a>` 的另一个作用便是链接 `E-mail` 地址。使用 `mailto` 可以链接收件人地址，详细如下：
```html
<a herf="mailto:lihongyuan@lewanny.com;lihongyuan@163.com?cc=1016201450@qq.com&bcc=1016201450@qq.com&subject=邮件主题&body=邮件内容">
其中 mailto: 为邮件地址，浏览器将会调用默认的邮件软件填入。
cc: 抄送地址。
bcc: 密件抄送地址。
多个收件人、抄送人可用 ； 隔开。
subject= 邮件的主题。
body= 邮件的内容。
```


#### 网页插入图片
可以使用 `<img>` 标签为网页插入图片。
其中 src 用来标记图片的位置。alt 指定描述文本，当图片下载失败或不显示时显示。title 图片可见时的描述。图像可以是 GIF，PNG，JPEG 的图像文件。
```html
<img src="xx.png" alt="图片下载失败时的提示" title="图片提示文本">
```



### 与浏览者交互，表单标签
#### 使用表单标签和浏览者交互
网站通过表单标签和用户进行交互 `<form>` ，表单可以把浏览者输入的数据传到服务器，这样服务器就可以处理表单然后再传数据回来。
```html
<form method="传送方式 post/get" action="服务器文件，如一个 PHP 页面 save.php">
```

注意：所有的控件（按钮、文本框、复选框等）等必须放在 `<form></form>` 之间，才可以上传到服务器。
```html
<form method="post" action="save.php">
      <label for="username">用户名:</label>
      <input type="text"  name="username" id="username" value="" />
      <label for="pass">密码:</label>
      <input type="password"  name="pass" id="pass" value="" />    
      <input type="submit" value="确定"  name="submit" />
      <input type="reset" value="重置" name="reset" />
</form>
```


#### 文本输入框和密码输入框
当用户输入时，可以使用文本输入框，当然文本输入框也可以转换为密码输入框。
```html
<form>
<input type="text/password" name="名称" value="文本" placeholder/>
</form>
```

`type` 为 text 时为文本输入框，为 password 时为密码输入框。
`name` 为文本框命名，以备后台 ASP 、PHP 使用。
`value` 文本框的默认显示内容，起到提示性作用。
`placeholder` 默认的提示性文字。

```html
<form>
姓名：<input type="text" name="账号输入框" value="请输入您的用户名" />
<br />
密码：<input type="password" name="密码输入框" value="请输入您的密码" />
</form>
```


#### 文本域支持多行输入
当需要多行输入时可以使用文本域。
```html
<textarea rows="行数" cols="列数">默认文本</textarea>
<form method="post" action="save.php">
	<label>联系我们：</label>
	<textarea rows=33 cols=12>在这里输入您的内容...</textarea>
</form>
```
`rows` 为行数，可用 CSS 样式中的 width 来代替。
`cols` 为列数，可用 CSS 样式中的 height 来代替。


#### 单选框和复选框
网页中为减少用户的操作，可以为用户提供单选框和复选框两种。
```html
<input type="radio/checkbox" value="值" name="名称" checked="checked" />
```

`type` 为 radio 时为单选框，为 checkbox 时为复选框。
`value` 提交到服务器的值，供后台 PHP 程序使用。
`name` 为空间名，供后台 ASP、PHP 程序使用。
`checked` 被设置为 checked=”checked” 时，该项默认被选中。

```html
<form name=""iForm method="post" action="save.php">
	你是否喜欢编程？<br />
	<input type="radio" name="codeLove" value="喜欢" checked="checked" /> 喜欢
	<input type="radio" name="codeLove" value="非常喜欢" />非常喜欢
	<input type="radio" name="codeLove" value="有点喜欢" /> <br />有点喜欢
	你喜欢的运动？<br />
	<input type="checkbox" name="sportLove" value="羽毛球" />羽毛球
	<input type="checkbox" name="sportLove" value="篮球" />篮球
	<input type="checkbox" name="sportLove" value="游泳" /> 游泳
	<input type="checkbox" name="sportLove" value="跑步" />跑步
</form>
``` 

注意：同一组的单选按钮，name 值一定要保持一致，如上面的 name 值都为 codeLove 。



#### 下拉列表来节省空间
```html
<form name="iForm">
	<lable>你的爱好</lable>
	<select>
		<option value="运动">羽毛球</option>
		<option value="音乐">音乐</option>
		<option value="旅游">旅游</option>
		<option value="读书" selected="selected">读书</option>
		<option value="游戏">游戏</option>
	 </select>
</form>
```

`value` 为提交的值，标签之间为显示的值。
`selected` 值为 selected 时，该项默认为选中项。



#### 下拉列表多选
下拉列表也可进行多选操作。在 `select` 标签中设置 `multiple="multiple"` 即可实现。按键盘 control/command 键即可多选。
```html
<form name="iForm2">
	<label>你的爱好</label>
	<select multiple="multiple">
		<option value="运动">运动</option>
		<option value="音乐">音乐</option>
		<option value="旅游">旅游</option>
		<option value="读书">读书</option>
		<option value="旅游">游戏</option>
	</select>
</form>
```


#### 使用提交按钮来提交数据
表单中有两种按钮，一个是提交，一个是重置。
```html
<input type="submit/reset" value="提交"/>
```

`type` 只有当 type 值设置为 submit 时，按钮才有提交作用，type 值为 reset 时为重置作用。
`value` 按钮上显示的文字。

```html
<form method="post" action="save.php">
	<label>姓名：</label>
 	<input type="text" name="myName" value=" "/>
 	<input type="submit" value="提交" name="submit" />
 	<input type="reset" value="重置" name="reset" />
</form>
```


#### form 表单中的 label 标签
`label` 不会呈现任何特殊效果，它为鼠标用户改进了可用性。如果你在 label 内点击文本，就会触发该控件。就是说，当用户单击选中该 label 标签时，浏览器就会自动将焦点转到和标签相关的表单控件上（就自动选中和该label标签相关连的表单控件上）。
```html
<label for="控件的 id 名称">
```

注意：label 的 for 属性的值应与相关控件 `id` 属性值相同。
```html
<form>
	<label for="male">男性</label>
	<input type="radio" name="gender" id="male" />
	<label for="female">女性</label>
	<input type="radio" name="gender" id="female" />
	<label for="email">输入您的邮箱地址</label>
	<input type="text" id="email" placeholder="Enter email" />
</form>
```

未完待续…
