---
title: hexo从GitHub上克隆到本地-恢复使用
top: 1
series: Hexo使用方法
date: 2025-05-10 10:43:44
categories: 
- 技术
- Hexo
tags:
- github
- hexo
---
<!-- toc -->

# hexo从GitHub上克隆到本地-恢复使用
## 一、配置环境

1.安装git

`https://git-scm.com/downloads`

2.Git与远程库进行SSH授权(点击查看教程Git的安装-与远程仓库GitHub配置)

3.安装node.js

`https://nodejs.org/en/` 

## 二.配置本地博客
1.克隆GitHub上保存的hexo网站原文件

`git clone 你的仓库地址`

2.安装hexo

进入克隆的目录，执行：

`npm install -g hexo-cli`

3.生成缺少的网站文件


```
npm install
npm install hexo-deployer-git --save
```

## 三.生成、部署

`hexo clean && hexo g && hexo d`

## 备份
克隆gitHub上面生成的静态文件到本地

git clone https://github.com/yourname/hexo-test.github.io.git
把克隆到本地的文件除了git的文件都删掉，找不到git的文件的话就到删了吧。不要用hexo init初始化。

将之前使用hexo写博客时候的整个目录（所有文件）搬过来。把该忽略的文件忽略了

touch .gitignore
创建一个叫hexo的分支

git checkout -b hexo
提交复制过来的文件到暂存区

git add --all
提交

git commit -m "新建分支源文件"
推送分支到github

git push --set-upstream origin hexo
到这里基本上就搞定了，以后再推就可以直接git push了，hexo的操作跟以前一样。

今后无论什么时候想要在其他电脑上面用hexo写博客，就直接把创建的分支克隆下来，npm install安装依赖之后就可以用了。

克隆分支的操作

git clone -b hexo https://github.com/yourname/hexo-test.github.io.git
因为上面创建的是一个名字叫hexo的分支，所以这里-b后面的是hexo，再把后面的gitHub的地址换成你自己的hexo博客的地址就可以了。

这样做完了以后，每次写完博客发布之后不要忘了还要git push把源文件推到分支上。

作者：砖用冰西瓜
链接：https://www.jianshu.com/p/beb8d611340a
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。