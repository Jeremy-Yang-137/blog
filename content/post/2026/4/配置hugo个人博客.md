---
date: '2026-04-11T21:27:32+08:00'
draft: false
title: '配置hugo个人博客'
---

hexo的主题我都不太喜欢。最近刚好看上了hugo这边PaperMod这个主题，于是转而使用hugo作为个人博客框架。

hugo和hexo的原理一样，都是把静态站点生成到./public里。

新建文章：`hugo new post/2026/test.md`（在./content里）

`content/archives/_index.md`：归档页面的占位符，PaperMod主题自动填充。

`content/post`：文章，可以分不同文件夹。

`content/about.md`：关于页面。

启动调试服务器：`hugo serve`

## 自定义主题

我使用的主题是PaperMod。

调整字体：新建一个assets/css/extended/custom.css，用!important强制覆盖原来的样式。

在hugo.yaml中自定义站点标题、导航栏、是否显示词数统计等。
