---
title: Hexo 博客文章提交 Baidu 和 Google 收录
date: 2018-01-14 19:51:30
tags: 
categories: Hexo
---

我们把 Hexo 托管在 Github 上，但是 Github 毕竟是国外的，访问速度上还是有点慢，并且百度的爬虫无法趴到 Github 上的数据，提交百度收录也自然是不存在的。所以同时部署了一套在国内的托管平台，也就是 [Coding](https://coding.net/) (也就是之前的 Gitcafe，部署方法和 Github 雷同据不写了)。这样国内访问的时候解析到 Coding，国外访问的时候解析到 Github，访问效率和文章收录就全部都搞定了。

为什么要提交文章到 Baidu 和 Google 收录呢，因为如果不提交的话通过对应的搜索引擎是搜索不到你的文章的，别人没法看到。提交收录成功后等待一段（大概15天左右，Google 会快些）就可以在晚上搜到你的文章了，我以 Baidu 和 Google 为例，记录下提交 Hexo 文章到 搜索引擎收录的方法。

<!--more-->

### 收录情况
打开浏览器的输入 `site:www.xxx.com` 即可查询，如果没有搜索到对应内容，就表示没有收录。


### 验证网站
网站验证目的是为了证明你是该网站的持有者，分别登录到 [Google Search Console](https://www.google.com/webmasters/tools/home) 和 [百度站长资源管理平台](https://ziyuan.baidu.com/site/index) 添加你的域名，然后一步一步验证就好。

方法一般分为 HTML 文件验证、HTML 标签验证、CNAME 验证等，其实就是一串类似于 `RTIrgNNg7m` 的密钥，将其放在你的网站主页就好，网站上写的都比较详细，就不再记录了。


### 站点配置
打开 Hexo 博客`站点配置文件`，添加以下两行。
```objc
google_site_verification: xxxxxxx
baidu-site-verification: xxxxxxx
```
执行编译命令 `hexo d -g` 。

注意 Google 的符号是 `_`，而百度为 `-`，其中 xxxxxxx 为在两个网站上获取的 HTML 标签内容。(即密钥)



### 添加站点地图
首先安装站点地图生成插件，打开终端分别输入以下命令。
```xml
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```

在博客的 `站点配置文件 _config.yml` 中添加以下代码。
```xml
# 自动生成sitemap
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml
```

再次执行编译命令 `hexo d -g` 。这时候在博客的public文件夹下面就会出现站点地图 `sitemap.xml` 和 `baidusitemap.xml` 了。

<font color=red>这里有一点需要注意</font>，打开两个站点地图文件，会发现里面的网址全部是 `http://yoursite.com/`，不是我们自己的域名，这样显然是不性的。打开 `站点配置文件 _config.yml` 搜索 `url:` 字段，将 http://yoursite.com/ 替换成你的域名，然后重新 `hexo d -g` 编译部署即可。

### 提交站点地图
经过上面的配置后，回到[Google Search Console](https://www.google.com/webmasters/tools/home) 和 [百度站长资源管理平台](https://ziyuan.baidu.com/site/index) ，选择提交 `Sitemap`，按照示例的格式提交即可。

提交完成后如果没有报错显示正常就代表提交成功了，但短时间内还是无法搜索到你的博客的，接下来等搜索抓取收录即可。另外 Baidu 和 Google 都是可以配置自动提交的（手动效率最高），按照网站上的方法一步一步操作就好。还有如果你和我一样用的是 `Next` 主题，只需要将 `主题配置文件` 中的 `baidu_push` 设置为 `true`，就可以完成百度自动推送了。

关于网站 seo 这一块的内容还是很多的，如果维护好自己的站点看似简单其实也是很复杂的，就先写到这里，有时间的话会继续更新一些进阶的 seo 优化方法。待续......