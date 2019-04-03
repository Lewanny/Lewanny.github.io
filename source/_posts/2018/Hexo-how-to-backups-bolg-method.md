---
title: Hexo 如何备份好你的博客
date: 2018-01-21 21:20:31
tags: 
categories: Hexo
---

无论什么时候数据备份都是相当必要的，不要等出现意外情况的时候数据丢失就追悔莫及了，比如我自己...。早期建站的时候就考虑过数据源码备份的事情了，但是后来把博客转移后，也就没再做了。

那么要怎么把博客源码高效方便的备份好呢？如果你想用 U 盘或者网盘，把文件从一台电脑拷贝到另一台电脑也是可的，只不过麻烦的很。解决方案的基本思路是，在你部署博客的 Github 仓库 `username.github.io` 新建一个 `hexo` 分支，用来提交和保存博客源码，默认的 `master` 分支用来部署静态网页，两个分支，各司其职。

可以参考我的仓库 [Lewanny.github.io](Lewanny.github.io)，两个分支一个 `master`，另一个 `hexo`。

<!--more-->


### 备份方法

1. 克隆 Github 上用来部署静态网页的 `username.github.io` 项目文件到本地。
```git
git clone https://github.com/yourname/xxx.github.io.git
```

2. 删除文件夹里除了 `.git` 的其他所有文件。

3. 把 hexo 项目文件下的所有文件全部复制过来，或者复制 `_config.yml`，`themes/`，`source/`，`scaffolds/`，`package.json`，`.gitignore` 这些文件即可。

4. 检查下里面是否有 `.gitignore` 文件，如果没有就输入 `touch .gitignore`，创建一个即可。

5. 新建名为 `hexo` 的分支并切换到这个分支上。（分支名字随意的）

6. 提交复制过来的文件到暂存区，探后提交并推送分支到 Github 即可。
```git
git add . 
git commit -m "新建备份分支"
git push origin hexo
```

这样一来，`username.github.io` 仓库就有 `master` 分支和 `hexo` 两个分支了，分别保存静态网页和源文件。

备份完成后，Hexo 更新的操作不便（不必切回到 master 分支） `Hexo d -g`，但不要忘记每次更新博客后执行 `git add .` 、 `git commit -m ""`、 `git push origin hexo` 重新提交源文件到备份分支上。


### 恢复方法
重装电脑后，或者换了其他电脑后，想要在其他电脑上操作博客，只要把备份的源文件克隆下来，重新安装 Hexo 环境，然后执行 `npm install` 安装依赖之后就可以了。

1. 安装 [git](https://git-scm.com/download/win) 。

2. 安装 [Node.js](https://nodejs.org/en/) 和 [npm](https://www.npmjs.com/) 。

3. 将备份的源文件内容拷贝至本地。
```git
git clone -b hexo https://github.com/yourname/xxx.github.io.git
```

4. 在文件夹内执行命令 `npm install hexo --save` 安装依赖即可。

操作完成后，整个博客就恢复完成了。更新或修改博客后依旧是执行 `hexo d -g` 更新不是，`git add .` 、 `git commit -m ""`、 `git push origin hexo` 提交源文件备份。


	
		
