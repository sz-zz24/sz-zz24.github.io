---
title: "用Git Pages加Hugo搭建个人博客全记录"
date: 2021-03-10T16:01:23+08:00
lastmod: 2021-03-11T16:01:23+08:00
draft: false
tags: ["Git Pages", "Hugo", "个人博客"]
categories: ["生活杂记"]
author: "clavenzhang"

weight: 1

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
# comment: false
# toc: false
mathjax: true
---
## 前言
在互联网大厂工作的第5个年头，危机该来还是要来了。因为公司业务调整，进了业务部门之后，发现外面似乎也那么风调雨顺。其实危机感一直有，但是人都是惰性的，认识到自己的不足，可是如果没有到致命的阶段，都是会保持无动于衷的。总有一些冠冕堂皇的理由来安慰自己，就这样在岁月蹉跎之下，没有成长，每次都在面临考验的时候反问自己，之前这么多的时间都干啥去了。博客是一个非常好的东西，可以不断记录自己的成长过程。以及关键时候，可以找到那些该死的，自己明明之前知道但是现在却怎么也记不起来的东西。好记性不如烂笔头是有科学依据的，人的大脑没有办法容纳那么多实时的/短期的和长期的记忆数据。有记录的东西，可以快速唤醒大脑内部沉睡的记忆。

为了记录自己的东西，我开始调研各种博客平台，发现github可以直接托管博客，简直不要太爽。在其他平台直接写自己的博客确实可以省掉不少麻烦，不用自己折腾。但是，用git有好几个方面的优势：第一，不用额外开一个账号，各种平台的账号太麻烦了，能少一个是一个。第二，可以直接用代码来管理自己的网站，随意div，非常酷。第三，完全免费，不需要购买服务器，也不需要自己担心环境/系统维护和文件存储等一系列麻烦的事情。基于这些原因，于是我开始了使用git pages搭建个人博客之旅。网上教程也比较多，但是中间还是踩过一些坑，下面也来记录一下自己搭建的整个过程。
## 如何使用GitHub Pages
首先，你得有一个github的账号，然后在个人主页创建一个新的repository。进入页面后，在repository name的位置填写域名，格式是username.github.io，其中username就是你的github账号，这个地方必须是这样的格式，否则网站不会生效。

创建成功之后，你可以在右上角的settings里面的github pages的选项，选择一个github官方提供的主题，选择好之后commit, github就会帮你生成网站，然后你在浏览器里面输入username.github.io就可以看到效果了。当然，这个只是最简单的第一步，如果你想要做一个内容丰富的博客，我们接着往下看。
## 配置自定义域名
**购买自己的域名**

github pages提供免费为自定义域名开启HTTPS的功能，并且大大简化了操作的流程，现在用户已经不需要自己提供证书，只需要将自己的域名使用CNAME的方式指向自己的github pages域名即可。这个地方很多教程都说的不清楚，直接跳过了需要自己购买域名的这一步，让人很是摸不着头脑，可能也跟我自己的经验有关系，因为之前没有买过，所以不太懂。可以去各种云服务提供商，比如腾讯/华为/阿里等，在他们的域名注册服务页面选择自己喜欢的域名，进行注册，以腾讯云为例：

**进入注册页面**

![域名注册](/blog/2021/tencent_dns.png)

**查询域名**

![域名注册](/blog/2021/tencent_dns_1.png)

这里面包含了各种后缀的顶级域名的注册情况，你可以根据自己的情况来进行购买，一般购买.com和.net应该就差不多了。这个地方是真的头疼，很多域名都被抢注了，稍微有点含义的词几乎都没有了。当时，我光注册域名这一块就花了一天时间，汗。

**域名配置**

域名买好之后，就可以开始配置域名转发到你自己的网站了。找到我的域名，在dns解析里添加一条解析记录，例如我选择添加子域名z24z.com通过CNAME的方式指向我刚刚自定义的git pages域名sz-zz24.gitHub.io（ping一下它对应的ip地址）。添加完成后等待dns解析的生效（dns的解析记录生效到全球可能需要几分钟时间）。

![域名注册](/blog/2021/tencent_dns_2.png)

经过上面的配置，之后重新回到最开始进入过的settings界面，找到github pages的设置，在custom domian里面填入我们刚刚建立的子域名，点击保存。保存后github需要一定的时间生成证书并确认域名的解析是否正常，我们只需要耐心等待即可，成功后显示结果如下：

![github_pages](/blog/2021/github_pages.png)

然后，你就可以在网站里面输入你的域名，然后看到网站的效果了。这样，网站代码托管就完成了。接下来，我们看下怎么样来构建个人的博客内容。
## 构建网站
我们需要利用静态模板系统来让生产接管博客的文章的生成，然后才可以安静地专注博客的内容。这里面也有很多的选择，包括jekyll/hexo/hugo等等，最终选择了基于go实现的hugo，首先当然是因为hugo功能强大，不需要有额外的其他组件依赖。同时，还具备主题库丰富，更换简单，网站生成速度快等优势。话不多说，下面我们就来看一下怎么操作。

**环境准备**

首先，安装hugo：初步使用hugo的话，只需要跟随官网的文档过一遍quick start就可以了解基本的安装、使用方法了。这里根据我自己的经历也进行简单的说明。在mac系统，通过包管理工具homebrew可以非常简单的安装hugo。
```
brew install hugo
```
**建立新站点**

接下来从终端进入到你想要放置博客站点内容的目录下面使用来建立站点。
```
hugo new site myblog
```
该命令会在当前目录下新建一个名为myblog的文件夹。你所有的站点文件都会在这个文件夹下面存放。常用目录用处如下

| 子目录名称 | 功能 |
| ------------ | ---------------------------------------------------------------------- |
| archetypes | 新文章默认模板 |
| config.toml | Hugo配置文档 |
| content | 存放所有Markdown格式的文章 |
| layouts | 存放自定义的view，可为空 |
| static | 存放图像、CNAME、css、js等资源，发布后该目录下所有资源将处于网页根目录 |
| themes | 存放下载的主题 |

使用下面的命令生成新的文章草稿：
```
hugo new posts/first-post.md
```
在content目录中会自动以archetypes/default.md为模板在content/posts目录下生成一篇名为first-post.md的文章草稿。或者直接在content里面手动创建mardown文档或者其他类型的内容。在这里建议使用hugo的new命令创建，因为根据主题不同，使用new命令创建的文件会包含简单的模版框架。例如：
```
---
title: my-first-post
date: 2020-06-13T19:45:22+08:00
lastmod: 2020-06-13T19:45:22+08:00
author: Author
cover: /post/xxx-cover.jpg
categories: ["技术"]
tags: ["Hugo", "GitHub"]
draft: true
---
```

**使用主题**

站点生成之后，如果没有主题，就是空白的文档，这样肯定是不行的。与其他的站点工具不同，hugo没有默认的主题，需要先添加一个主题才能新建文章。hugo的官网上有很多的主题可选。选定一个喜欢的主题之后，需要将其下载到myblog文件夹中。在主题说明的页面中点击"download"的按钮，会进入到对应的github页面中。有很多种方式可以将主题文件下载，并放置到myblog/themes/yourtheme文件夹中（yourtheme是主题的名称，可以在该主题的github仓库的页面看到）。在这里为了使用git对站点进行管理，实现在不同的设备上方便的对站点进行维护，我们使用git的submodule功能。
```
cd myblog
git init
git submodule add https://github.com/budparr/hugo-theme-11.git themes/yourtheme
```
接下来，我们需要在配置文件中指名站点所使用的主题。打开config.toml直接编辑或者使用命令。
```
echo 'theme = "yourtheme"' >> config.toml
```
配置文件中还有其他的可配置内容，这些我们后面再看。hugo的配置文件和文章中引用图片都是从static作为根目录的。写文章的时候可以直接从static目录引用。

**开启hugo本地服务**

所有的配置都在站点根目录下的cofing.toml文件下，其中baseURL的配置就是站点生成的网址。默认本地的1313端口。
```
baseURL = "http://localhost:1313"
```
执行命令
```
hugo server -D
```
然后打开网址，就可以看到渲染的内容。这个地方说明一下，在发布到环境的时候，把这个配置改成正式环境的地址，也就是上面买的域名地址。

**配置文件**

打开配置config.toml可以看到很多的参数可以配置，这里只描述最基本的内容，不同的主题可能会支持不同的参数配置，具体请看对应主题的说明文档。baseURL是站点的域名。title是站点的名称。theme是站点的主题。还有关于评论和打赏的相关配置，这些配置都可以参考官网主题的说明。

**发布**
每次发布的时候，都需要先执行hugo，把新写的文档按照主题进行渲染，所有生成的文件默认都在当前pulic的子目录下，可以在config里面配置到其他目录。然后把所有新的文件提交到github。提交代码之后，要等一段时间才生效。
## 代码托管
目前github已经把之前的master分支改为main分支了，所以username.github.io的这个代码库，main分支我们就放一些项目介绍的东西就可以。使用master分支来管理网站托管的内容。

```
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/xxx/username.github.io.git
git push -u origin master
```
至此所有源文档就都push到github上了。然而此时github对待这些源文档跟其他任何普通的repository中的代码并没有任何不同，并不会将public子目录中的网页托管在github pages上。

参考hugo官方文档，可以选择以下两种方式让github pages加载我们想要托管的/public子目录中的网页：

* 配置Hugo将网页生成在名为/docs的子目录中，然后直接push到master branch
* 仍然使用默认的/public子目录存储网页，再单独建立一个gh-pages branch

第一种方案的好处在于一次push即可将源文档和对应生成的网页文档都发布到github，操作非常简单。所需要的仅是在config.toml中添加如下一行配置：
```
publishDir = "docs"
```
使得生成的网页默认保存在/docs子目录下。这种方式管理简单，一目了然，所以直接用了这种方式。如果需要用到第二种方式，可以查阅相关文献，此处不做记录。

自此运行hugo命令后生成的网页文件将保存在/docs子目录下。将所有文档push到github的master branch，进入github对应repository的settings标签菜单，在github pages选项的source栏选择master branch /docs folder, 详情见前面域名配置的图。等待片刻即可访问域名看到之前用hugo生成的网页了。

这里面还有一个坑，大部分的内容都可以通过配置来管理，但是有一个docs的东西，怎么也找不到，它的删除方法是：将doc菜单下的几个帖子对应的markdown文件打开，删除或者注释下面所示的代码设置。
```
menu:
  main:
    parent: "docs"
    weight: 3
```
这也是一种加功能菜单的方法，需要的时候可以用起来。

## 后记
至此我的博客搭建记录就完成了，基本满足了我写文章的各项需求。也作为成长的一个新起点吧，什么事情都要从动起来开始，加油！