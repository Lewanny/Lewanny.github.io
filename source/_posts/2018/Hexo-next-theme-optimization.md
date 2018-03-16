---
title: Hexo Next 主题的优化
date: 2018-01-12 17:37:08
tags: 
categories: Hexo
---

基础框架全部搭好之后，自然就是关于一些优化和个性化的事情了。关于 Next 主题的优化，如设置主题样式、字体、头像、第三方统计、评论、分享等在 [NexT 使用文档](http://theme-next.iissnan.com/) 里都有极详细的介绍，这里不一一介绍了。只记录一些文档中没有提及的点。

#### 第三方推荐
搜索：[Local Search](http://theme-next.iissnan.com/third-party-services.html#local-search) 简单方便，安装即可使用。
统计：[不蒜子统计](http://theme-next.iissnan.com/third-party-services.html#analytics-busuanzi)，简单易用推荐使用。
评论：[来必力评论](http://theme-next.iissnan.com/third-party-services.html#livere)，目前好像也只有这个可用了，之前还用过多说评论和网易云跟帖。
<!--more-->


#### 侧边栏社交小图标设置
打开主题配置文件 _config.yml 搜索 `social:`，在 [图标库](https://fontawesome.com/icons?d=gallery) 找自己喜欢的小图标，并将名字复制在对应的 || 后，保存即可：

```objc
social:
  GitHub: https://github.com/Lewanny || github
  微博: https://weibo.com/u/2951067307 || weibo
  简书: https://www.jianshu.com/users/f0040a970739 || heartbeat 
```


![](http://omkug1guu.bkt.clouddn.com/Hexo-next-theme-optimization/social_icon.png)


#### 添加 about 页面
进入到博客根目录下，执行以下命令：

	$ hexo new page "about"
	
完成后会在 `/source/about` 目录下生成一个 index.md 文件。打开 index.md 文件，在 data 下添加 `type: "about"`，然后编辑自己想要展示的内容即可。（注意将主题配置文件中的 `menu:` about 页面打开）。


#### 添加网易云音乐播放
使用 `iframe` 标签播放即可。
	
	<iframe frameborder="no" border="0" marginwidth="0"
	 width="300" height="86" 
	 src="https://music.163.com/outchain/player?type=2&id=5253801&auto=0&height=66"></iframe>  // 到网易云中复制即可


<iframe frameborder="no" border="0" marginwidth="0"
	 width="300" height="86" 
	 src="https://music.163.com/outchain/player?type=2&id=5253801&auto=0&height=66"></iframe>
	 
如果想放在侧栏里面播放，将代码 加入到 `hexo/themes/next/layout/_macro` 目录中的 sidebar.swig 文件中即可。位置如下：

![](http://omkug1guu.bkt.clouddn.com/Hexo-next-theme-optimization/music_position.png)


#### 设置网站的 Favicon 图标
准备一个常见格式名(如.jpg、.png等)的图片作为备选 favicon，选择一个 favicon 制作网站完成制作，例如：[比特虫](http://www.bitbug.net/)。

如下修改主题配置文件即可：
```
favicon:
  small: /images/favicon_16x16.ico
  medium: /images/favicon_32x32.ico
  apple_touch_icon: /images/favicon_128x128.ico
  safari_pinned_tab: /images/favicon_128x128.ico
```

未完待续

<!--#### 添加 "high 一下"


#### 绑定独立域名
购买域名
在你的域名注册提供商那里配置DNS解析，获取GitHub的IP地址点击，进入source目录下，添加CNAME文件

	$ cd source/
 	$ touch CNAME
	$ vim CNAME # 输入你的域名
	$ git add CNAME
	$ git commit -m "add CNAME"
	
	
#### -->


