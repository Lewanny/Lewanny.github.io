---
title: 使用 Hexo 搭建个人博客并部署到 Github Page
date: 2018-01-11 17:14:04
tags: Hexo
categories: Hexo
---

前段时间由于电脑突然故障，导致所有本地数据全部丢失，所以不得不重新把个人博客搭起来。还好当时把博客分别部署到了 Coding 和 Github，省去了一些麻烦事，只是 md 文件都要重新写一遍了。这里重新记录下相关的过程。有备无患，后面有时间的话会陆续总结先优化为、同时部署到Coding 和 Github 、提交百度、Google 收录、SEO 搜索等。

`Hexo` 是一个快速、简洁且高效的博客框架。而 Github 是一个免费的代码托管工具，利用 Github Page 可以免费创建一个静态网站。下面将介绍如何使用 Hexo 和 Github，在 mac 环境下搭建静态博客。

<!--more-->

### Hexo 环境配置
#### Node.js
用来生成静态页面，可到 [Node.js 官网](https://nodejs.org/en/) 下载推荐版本，默认配置一路安装即可。

#### Git
用来将本地 Hexo 内容提交到 Github 上。Xcode 自带 Git。如果没有 Xcode 可以参考 [Git 官网](https://git-scm.com/download/win) 下载安装。


### 安装 Hexo
Node.js 和 Git 都安装好后就可以正式安装 Hexo 了，终端执行如下命令：

	$ sudo npm install -g hexo
	
> sudo:linux 系统管理指令     -g:全局安装


初始化 Hexo，终端 cd 到一个你选定的目录，执行 `hexo init` 命令：

	$ hexo init blog
	
> blog 是你建立的文件夹名称。

cd 到 blog 文件夹下，执行如下命令，安装 `npm`：

	$ npm install

执行如下命令，开启 Hexo 服务器（结束为 control+c）：

	$ hexo s

此时，浏览器中打开网址 [http://localhost:4000](http://localhost:4000)，如果能看到如下页面，Hexo 本地初始化即完成：

![](http://omkug1guu.bkt.clouddn.com/Hexo-build-yourown-blog-with-hexo/Hexo_success.png)



### 关联到 Github 
#### 添加 ssh key 到 Github
##### 检查 SSH keys 是否存在 Github
执行如下命令，检查 SSH keys 是否存在。如果有文件 id_rsa.pub 或 id_dsa.pub ，则直接进入步骤 1.3 将 SSH key 添加到 Github 中，否则进入下一步生成 SSH key。

	$ ls -al ~/.ssh

##### 生成新的 ssh key
执行如下命令生成 public/private rsa key pair，注意将 your_email@example.com 换成你自己注册 Github 的邮箱地址。、

	$ ssh-keygen -t rsa -C "your_email@example.com"

接下来会提示输入密码路径，按 3 次回车，密码为空。最后会在相应路径下`（~/.ssh/id_rsa.pub）`生成 d_rsa 和 id_rsa.pub 两个文件。

##### 将 ssh key 添加到 Github 中
Finder 前往文件夹 `~/.ssh/id_rsa.pub` 打开 id_rsa.pub 文件，里面的信息即为 SSH key，将这些信息复制到 Github 的 Add SSH key 页面即可。

进入Github –> Settings –> SSH keys –> add SSH key，Title 任意，将复制的内容粘贴到 Key 里，点击 Add key 按钮即可。

![](http://omkug1guu.bkt.clouddn.com/Hexo-build-yourown-blog-with-hexo/Hexo_hss%20.png)


#### 创建仓库
登录 Github 帐号，新建仓库，命名必须为 用户名.github.io 固定写法，如Lewanny.github.io,如下图所示：

![](http://omkug1guu.bkt.clouddn.com/Hexo-build-yourown-blog-with-hexo/Hexo_github_repo.png)

本地的 blog 文件夹下内容为：

![](http://omkug1guu.bkt.clouddn.com/Hexo-build-yourown-blog-with-hexo/Hexo_root_catalog.png)


#### 配置文件
打开 `_config.yml` 文件，里面是博客的主体配置项，找到 `deploy:` 配置，修改如下：
```
deploy:
   type: git
   repository: https://github.com/Lewanny/Lewanny.github.io.git
   branch: master
```
> 注意：在配置所有的 _config.yml 文件时（包括 theme）时，在所有的冒号 : 后边都要加一个空格，否则执行 hexo 命令会报错。

#### 部署文件
##### 生成静态页面命令
在 blog 文件夹目录下执行生成静态页面命令：

	$ hexo g		// 或者：hexo generate

> 此时若出现如下报错：
`
1 ERROR Local hexo not found in ~/blog
2 ERROR Try runing: 'npm install hexo --save'
`
>则执行命令：
`npm install hexo --save
`

##### 配置命令
再执行配置命令：

	$ hexo d		// 或者：hexo deploy

> 若执行命令 hexo deploy 仍然报错：`ERROR Deployer not found: git`。
> 则执行如下命令来安装 hexo-deployer-git： `npm install --save hexo-deployer-git`

再次执行 hexo generate 和 hexo deploy 命令 (或 hexo d -g，即部署前先生成静态界面) 即可完成 Github 关联。此时，浏览器中打开网址 [https://lewanny.github.io](http://Lewanny.github.io)（注意将 username 替换）能看到和打开 [http://localhost:4000](http://localhost:4000) 时一样的页面。 


### 安装 Theme
可以到 [Hexo 官网主题页](https://hexo.io/themes/) 去搜寻自己喜欢的 theme。这里以 [hexo-theme-next](https://github.com/iissnan/hexo-theme-next) 为例，[Next 官网](http://theme-next.iissnan.com/getting-started.html)。

终端 cd 到 blog 目录下执行如下命令：

	$ git clone https://github.com/iissnan/hexo-theme-next themes/next

将 blog 目录下 `_config.yml` 里 theme 的名称 landscape 修改为 next 即可。

终端 cd 到 blog 目录下执行如下命令(每次部署文章的步骤)：
```
	$ hexo clean    // 清除缓存文件 (db.json) 和已生成的静态文件 (public)
	$ hexo d -g     // 生成缓存和静态文件并重新部署到服务器 
```
	
至于更改 theme 内容，比如名称，描述，头像等去修改 `blog/_config.yml` 文件和 `blog/themes/next/_config.yml` 文件中对应的属性名称即可， 不要忘记冒号:后加空格。[NexT 使用文档](http://theme-next.iissnan.com/)里有极详细的介绍。



### 发表文章
终端 cd 到 blog 文件夹下，执行如下命令新建文章：

	hexo new "postName"    // postName为文章名字
	
名为 postName.md 的文件会建在目录 /blog/source/_posts 下。你当然可以用 vim 来编辑文章，这里推荐使用 [MacDown](http://macdown.uranusjr.com/)。

文章编辑完成后，终端 cd 到 blog 文件夹下，执行如下命令来发布：
```
	hexo generate    // 生成静态页面
	hexo deploy      // 将文章部署到 Github  // 或 hexo d -g
```

至此，使用 Hexo 搭建个人博客并部署到 GitHub 就基本完成了。后面会陆续更新出如何绑定独立域名、next 主题的优化和功能配置、同时部署博客到 GitHub 和 Coding、提交文章到百度谷歌收录、备份博客等（防止悲剧重演...）。






