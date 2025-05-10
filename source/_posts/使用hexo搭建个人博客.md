---
title: 使用hexo搭建个人博客
top: 2
series: Hexo使用方法
date: 2025-05-09 21:56:12
categories: 
- 技术
- Hexo
tags:
- hexo
- github
- 博客
comments: true
---
<!-- toc -->

# 使用hexo搭建个人博客
## 安装Hexo 

安装hexo之前，要先下载安装Node.js和Git，百度搜索找到下载即可。

Git下载地址: 

`https://git-scm.com/downloads`

Node.js下载地址:

`https://nodejs.org/en/` 

![Node](Node.png)

在本地建立一个文件夹，我的文件夹为D:github/hexotest。 

接下来可以进入这个文件夹右键Git Bash 或者通过命令号cmd进入这个文件夹 

输入如下命令： 

```
npm install hexo-cli -g #安装hexo
#初始化网站  
hexo init    
#下载hexo
npm install
#生成或 hexo generate      
hexo g
#启动本地服务器 
hexo s
```

重启命令, 更新并且上传：

`hexo clean && hexo g && hexo d` 

通过http://localhost:4000 查看效果 

如果无法显示，那么可能是端口被占用 

`hexo server -p 6666` 

指定端口启动，可看到网页了 

## 切换主题

默认主题为landscape，比较简陋，可以自己下载喜欢的主题，将下载的主题放到themes文件夹下，并且修改_config.yml可以实现主题的替换。

## 关联github
安装该扩展主要是为了支持hexo d 命令。

`npm install hexo-deployer-git --save`

编辑根目录下_config.yml文件 

```
deploy:
    type: git
    repo: git@github.com:secondtonone1/secondtonone1.github.io.git  #这里的网址填你自己的
    branch: main
```

## SSH keys的配置

在Git 命令行输入如下命令：

`ssh-keygen -t rsa -C "GitHub的注册邮箱"` 

Git显示：

```
$ ssh-keygen -t rsa -C "GitHub的注册邮箱"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):  (输入回车)
```

系统会要求你输入密码，这里设置的密码主要是在你提交Git时弹出密码认证，不想输入直接回车

```
Enter passphrase (empty for no passphrase): (输入回车或者密码) 
Enter same passphrase again: (输入回车或者密码)
```

## 配置ssh和github仓库
找到id_rsa.pub 并复制文件内容 

id_rsa.pub文件一般位于电脑用户配置文件夹下的一个.ssh文件下C:Users你的用户名.ssh 

登录Github并添加密钥 

进入github首页在右上角选择settings设置。

## 测试并链接到Git

链接Git

`ssh -T git@github.com`

以下为成功链接到Git

`Hi XXXX! You've successfully authenticated, but GitHub does not provide shell access.`

以后每次提交博文到github就不需要输入密码了，很方便。配置好后清除之前的记录，重新生成并提交

`hexo clean && hexo g && hexo d`

## 注意事项

### 注意

部分需要单独安装 hexo 的渲染包

`npm i hexo-renderer-swig`

### 推荐插件
#### 博文添加图片
`npm install hexo-asset-image –save`

修改文档_config.yml添加以下内容

```
marked:								
  prependRoot: true
  postAsset: true
```

然后就可以在md文件中使用, ![test](test.jpg)

如果图片无法显示可能需要修改hexo-asset-image插件，修改node_modules\hexo-asset-image\index.js：

```
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
    	var link = data.permalink;
	if(version.length > 0 && Number(version[0]) == 3)
	   var beginPos = getPosition(link, '/', 1) + 1;
	else
	   var beginPos = getPosition(link, '/', 3) + 1;
	// In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
	var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
		if ($(this).attr('src')){
			// For windows style path, we replace '\' to '/'.
			var src = $(this).attr('src').replace('\\', '/');
			if(!/http[s]*.*|\/\/.*/.test(src) &&
			   !/^\s*\//.test(src)) {
			  // For "about" page, the first part of "src" can't be removed.
			  // In addition, to support multi-level local directory.
			  var linkArray = link.split('/').filter(function(elem){
				return elem != '';
			  });
			  var srcArray = src.split('/').filter(function(elem){
				return elem != '' && elem != '.';
			  });
			  if(srcArray.length > 1)
				srcArray.shift();
			  src = srcArray.join('/');
			  $(this).attr('src', config.root + link + src);
			  console.info&&console.info("update link as:-->"+config.root + link + src);
			}
		}else{
			console.info&&console.info("no src attr, skipped...");
			console.info&&console.info($(this));
		}
      });
      data[key] = $.html();
    }
  }
});
```

#### 评论功能

`https://console.leancloud.cn/`

注册账号并实名后，进入控制台后主页点击创建应用，选择开发版，我们点击进入，查看左侧边栏的设置中的应用凭证部分，就能看到你的APP ID和APP Key了

修改_config_butterfly.yml，添加如下配置代码：
```
valine:
  appId: # leancloud application app id
  appKey: # leancloud application app key
  avatar: monsterid # gravatar style https://valine.js.org/#/avatar
  serverURLs: # This configuration is suitable for domestic custom domain name users, overseas version will be automatically detected (no need to manually fill in)
  bg: # valine background
  visitor: false
  option:
```


#### 文章置顶
支持置顶与隐藏文章的 Hexo 首页生成器插件。

在官方的首页生成器的基础上添加了使用 top 置顶文章和 hide 隐藏文章的功能。

在文章的front-matter区域里添加top: 1属性来把这篇文章置顶。数值越大，置顶的优先级越大。

首先卸载掉已经安装的 generator-index 类插件，然后安装本插件。

```
npm uninstall hexo-generator-index
npm install hexo-generator-index-custom --save
```

在 _config.yml 中新增或修改如下配置：

```
index_generator:
  path: ''
  per_page: 10
  order_by: -date
  pagination_dir: page
```

#### 字数统计
`npm i --save hexo-wordcount`

修改文档_config.butterfly.yml

```
wordcount:
  enable: true
  post_wordcount: true
  min2read: true
  total_wordcount: true
```

#### 本地搜索
`npm install hexo-generator-searchdb --save`

修改文档_config.yml添加以下内容

```
search:
  path: search.xml
  field: post
  content: true
  format: html
```

```
path：表示搜索后生成的文件路径，可以生成xml和json两种格式；将path设置为“content.json”就可以替代第二个插件。
field：表示搜索的范围，有“post、page和all”三种值。
    post：所有的文章；
    page：所有顶部导航选项的页面；
    all：所有的文章和顶部导航选项的页面。
content：是否包含搜索到的文章的全部内容。如果false，生成的结果只包括标题和创建时间这些信息，没有文章主体。默认情况下是true.
format：搜索到的内容、选项的格式。
    html(默认)：将html原文本缩略。
    striptags：将html原文本缩略，并删除所有标记。
    raw：记下每一篇文章或每一页的文字。
```

修改文档_config.butterfly.yml
```
# 本地搜索
local_search:
  enable: true
```

#### hexo-toc生成目录

`npm install hexo-toc --save`

修改文档_config.yml添加以下内容

```
toc:
  maxdepth: 3
  class: toc
  slugify: transliteration
  decodeEntities: false
  anchor:
    position: after
    symbol: '#'
    style: header-anchor
```

在 Markdown 文件中使用 TOC

在需要显示目录的地方，添加以下标记：



####  中文链接转拼音hexo-permalink-pinyin

``

修改文档_config.yml添加以下内容

```
permalink_pinyin:
  enable: true
  separator: '-' # default: '-'
```

#### 代码高亮

``

修改文档_config.yml

```
prism_plugin:
  mode: 'preprocess'    # realtime/preprocess
  theme: 'tomorrow'     #主题选项
  line_number: true    # default false
  custom_css:
```
