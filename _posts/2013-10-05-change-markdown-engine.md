---
layout: post
title: "Markdown引擎折腾记"
description: "change markdown engine"
category: code
tags: [jekyll , markdown ]
---

{% include JB/setup %}

Jekyll 支持 kramdown，maruku，rdiscount，redcarpet 等 Markdwon 渲染引擎，

其中maruku是默认的markdown引擎。安装不同的引擎很容易，
```gem install <引擎名>```，然后对照着编辑```_config.yml```即可。

最近在GitHub Pages里提交了新的文件之后，收到邮件"page build failed"，
本地测试发现是语言编码的问题，UTF-8和GBK冲突，

提示

```
Liquid error: incompatible character encodings: UTF-8 and GBK
```
可能和最近系统迁移到WIN8上游关系，之前也出现过，通过修改convertible.rb文件得到解决

方法如下：

编辑 E:\Ruby200\lib\ruby\gems\2.0.0\gems\jekyll-1.0.3\lib\jekyll目录下的convertible.rb文件

```
self.content = File.read(File.join(base, name))
```
改为

```
self.content = File.read(File.join(base, name),:encoding=>"utf-8")
```

详见[博文](http://www.cnblogs.com/aleda/articles/Jekyll-in-Windows-following-Chinese-encoding-problem-solutions.html)

而后编译博文时又出现一个问题

```
cannot load such file -- yajl/2.0/yajl (LoadError)
```
网上搜索了一圈，得知可能和我windows环境有关，这下可就没招了，鉴于我只是想用Jekyll，
没想过研究ruby，所有能绕就绕过吧，想到要不换一个渲染引擎。

切换引擎的方式很简单，第一次换成了rdiscount

```
gem install rdiscount
```
修改 _config.yml 加上一行：

```
markdown: rdiscount
```

更换引擎后，本地测试正常，push到Pages上，也能成功渲染。

仔细查看，发现一个问题，github Pages貌似不识别```标识代码块，
代码原样输出，页面很难看。

纳闷了半天，本地和github的配置一模一样，为何就不一致了。

网上搜罗了半天，看到[博文](http://www.soimort.org/posts/130/),估计是不是本地和pages的jekyll
版本不一致的原因，也看到Redcarpet是由GitHub自己人开发的，
一直以来它被用于在GitHub上渲染Markdown格式文本，想着要不切换到redcarpet好了，

和rdiscount一样的安装流程，
本地测试没有问题，push到Pages上面，也能正常展示。

虽然问题都得到的解决，但是总是觉得不识别```标识应该和rdiscount无关，因为没有看到任何文章提到这点，
并且我本地rdiscount渲染也是正常的。

昨天一天在各个引擎上折腾了一圈，问题终于得到了解决。

为了解决环境的问题，特地在Debian上也搭建了ruby环境，git+jekyll，但愿以后不用再出现编码以及各位
莫名的奇葩问题，但是要和各种源码编译，安装包等打交道，其乐无穷～～

