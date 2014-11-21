---
layout: post
title: "感觉这个用来写写自己的日志还不错"
description: ""
category:  [tech, thinking]
tags: []
---
{% include JB/setup %}
写写自己平时的工作日志还是不错的。

下午在完全不懂Ruby的情况下，用[Jekyll-Bootstrap](http://jekyllbootstrap.com)快速生成这个Blog页面，一试试还可以，于是把它push到自己在github的空间。

现在的访问地址是[yeahnoob.github.io](http://yeahnoob.github.io)，预计把原来购买的[blog.yeahnoob.com](http://blog.yeahnoob.com)域名定向到这个域名上，看起来会显得更好一点点。

Blog上添加日志内容分几步：

- 在clone的目录里 ，用下面的命令创建新的日志标题，并生成初始日志 *.md 文件到 ./_posts 文件夹中。

```shell
$ rake post title="new-title-name"
```

- 用文本编辑器打开新生成的 ./posts/*.md 文件，添加日志正文内容，我现在自己是在用VIM。
- 最后push修改到github的服务器，于是在yeahnoob.github.com地址上会显示新添加的日志内容。

作为个人写写平时学习工作的记录来使用，功能方面是完全足够，使用方式也很直观。
