---
title: Theme Allocation Of Next 
tags: 'Testing'
categories: Testing
date: 2020-10-01 17:23:37
abbrlink: 54882
---

 ![](http://theme-next.iissnan.com/assets/img/NextSchemes3.png)
有关hexo建站的教程，官网和一些博客介绍的很详细，基本上创建一个静态的博客是没有问题的，但是在使用主题后对主题进行一些列的配置就很麻烦，具体体现在各个文件的关联和繁杂的第三方。
<!--more-->

# hexo
 [hexo官网](https://hexo.io/zh-cn/docs/)

>Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub和Coding上，是搭建博客的首选框架。大家可以进入hexo官网进行详细查看，因为Hexo的创建者是台湾人，对中文的支持很友好，可以选择中文进行查看。

## 安装步骤
- Hexo搭建步骤
- 安装Git
- 安装Node.js
- 安装Hexo
- GitHub创建个人仓库
- 生成SSH添加到GitHub
- 将hexo部署到GitHub
- 设置个人域名
- 发布文章

具体的安装教程可以参考[hexo史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)

建站过程中有两点短板，先记录下来，后续跟进学习：
- git的相关语法
- css

**其中有关百度收录方面，使用CNAME是最方便的，直接使用域名解析到百度提供的地址即可**

**收录推送也只是简单的选择sitemap**

next内置了valine的评论系统，而valine使用cleanCloud的数据库，这就造成了一个数据方面的问题————cleanCloud每天要休眠一段时间，这就使评论系统有时候会出现显示不了的问题，所以果断舍弃，大道至简。

## next主题优化
- 参考
>[nexT官方文档](http://theme-next.iissnan.com/theme-settings.html)
 [next-主题优化](https://zhuanlan.zhihu.com/p/30836436)

再参考相关文章之后，主要添加了几个实用的优化：
- 搜索
- 文本字数和时间提醒
- 点击效果
- 卡片样式调整

还有一个就是使用了[腾讯公益的404页面](https://lucksun.work/404.html)

