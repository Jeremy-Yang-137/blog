---
date: '2026-04-05T21:55:11+08:00'
draft: false
title: '配置hexo个人博客'
---

hexo，是大一时候接触过的一位研究生学长推荐给我的博客框架。

也许是受他的影响？总之我一直对“个人博客”或“个人网站”这个东西念念不忘。

我写这个博客的目的不是真正为了给别人看。真实原因有以下几点：

1. “网站”向来是进行展现、表达的地方，而一个属于自己的网站就像是自己人格的镜像。我感觉，打理好自己的网站就像打理好自己的外表和住处一样自然。

2. 对于开发者来说，写作能力很重要。我想锻炼自己的写作能力，让写出来的文章有清晰的逻辑思维、语句不冗余，能够准确地传递我的想法。

3. 我想在自己折腾IT的路上留下一些记录，虽然大概率对未来没什么用，但有纪念意义。

4. 我想记录日常生活中的一些想法。

我对网站很挑剔。它不能有违背我审美原则的地方，更不能有别人的广告。于是，找现成的博客平台肯定是不行的。

我做了一些尝试。纯html+css+JavaScript太古老了，效率很低；mediawiki很庞大，有很多用不上的功能，并且要租轻量应用服务器，太贵了；wordpress也一样。于是，最后我就想到了hexo的方案。（我真的很喜欢mediawiki的风格，很可惜）

## 原理

hexo只是一个“生成器”，利用写成markdown格式的文章和指定的主题生成静态页面，并全部放在一个文件夹（./public）内。

真正提供托管服务的是github pages（或其它平台）。我可以将./public文件夹内的全部文件上传到github中自己一个名为`<github用户名>.github.io`的仓库内，然后就可以通过`https://<github用户名>.github.io/`这个网址访问上传的静态页面。

## 安装

第一步：用npm安装hexo-cli

第二步：创建博客

第三步：创建页面（会在source/_posts里创建一个md文件）

第四步：编辑新建的页面.md文件

如果想要实时查看更改，可以启动开发服务器（每次更改后需刷新页面）：
```bash
hexo s
```

## 一键部署

如果想要推送到github pages上，可以使用“一键部署”（需要安装hexo-deployer-git，并在_config.yml里配置）：

每次在确认要更新站点后，就只需执行：
```bash
hexo clean      # 删除上次生成的./public文件夹
hexo g          # 利用当前的文章和主题在./public文件夹下生成静态站点，包含所有的页面
hexo d          # 自动将./public中的内容上传到github上的“<github用户名>.github.io”仓库
```

## 自定义主题

我挑选的主题是icarus。

第一步：通过npm安装主题

第二步：在./_config.yml中设置主题

第三步：修改./_config.icarus.yml，删去不想要的部分（如文章大纲、广告位置、站点副标题），然后添加指向自己的logo、favicon、avatar的链接，并将github按钮链接到自己的github页面。

第四步：修改字体
```
node_modules/hexo-theme-icarus/include/style/base.styl  修改页面和代码字体
node_modules/hexo-theme-icarus/layout/common/head.jsx   修改字体CDN（from Google Fonts）
```

第五步：样式微调

同样在base.styl里，可以修改如页面背景颜色、字体大小、卡片边缘、阴影样式等。

参考：[Icarus用户指南](https://ppoffice.github.io/hexo-theme-icarus/Configuration/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/)
