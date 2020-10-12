---
title: 【Angular2】--配置开发环境
abbrlink: 9c4101a6
date: 2020-9-12 13:59:39
tags: [Angular2, 环境配置]
categories: Angular2
---



# Angular简介
先贴一下[官网](https://angular.cn/guide/setup-local)：https://angular.cn/guide/setup-local

首先Angular是一个mvc框架，它致力于**mvc代码解耦**，采用**model**,**controller**以及**view**方式去组织代码，Angular会将一个HTML页面分成若干个模块，每个模块都可以自己的scope，service以及directive，各个模块之间也可以进行通信，但是整体上结构是比较清晰的，就是说其代码组织方式是模块化的。
<!--more-->
angular的view可能仅仅是一个框架，对view的dom操作或者时间监听都是在directive中实现的，而且一般情况下很少自己直接去写Dom操作代码，只要你监听model。model发生变化后view也会发生变化。
**Angular特点：**

 - **数据的双向绑定**——这可能是其最激动人心的特性吧，view层的数据和model层的数据是双向绑定的，其中之一发生更改，另一方会随之变化，这不用你写任何代码！
 - **代码模块化**——每个模块的代码独立拥有自己的作用域，model，controller等。
 - **依赖注入**——将这种后端语言的设计模式赋予前端代码，这意味着前端的代码可以提高重用性和灵活性，未来的模式可能将大量操作放在客户端，服务端只提供数据来源和其他客户端无法完成的操作；
 - **测试驱动开发**——angularjs一开始就以此为目标，使用angular开发的应用可以很容易地进行单元测试和端对端测试，这解决了传统的js代码难以测试和维护的缺陷
# 安装步骤
- **Node.js** 
[官网](https://nodejs.org/zh-cn/)![node.js官网](https://img-blog.csdnimg.cn/20200909145958723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
安装完成后在**终端**中输入**node -v**，返回**版本号(例：v12.16.2)** 即表示安装成功。
- **npm包管理器**
Angular、Angular CLI 以及 Angular 应用都要依赖 npm 包来实现很多特性和功能。**npm会随着node的安装而一起安装**，在**终端**中输入**npm -v**返回**版本号(6.14.4)**
- **cnpm国内淘宝镜像(可选)**
终端输入：npm  install -g  cnpm --registry=https://registry.npm.taobao.org
- **Angular CLI（angular脚手架）**
终端输入：npm  install  -g  @angular/cli  或
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cnpm  install  -g  @angular/cli  （推荐使用cnpm）
验证安装是否成功：ng  v（出现以下即成功）![cli安装成功](https://img-blog.csdnimg.cn/20200909151606264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
- **编辑工具**
选择自己习惯的就好，个人比较喜欢[VS Code](https://code.visualstudio.com)，原因有几点：

**界面现代化，主题多样，可定制性高**
VSCode 不但可以换主题还可以换图标包，甚至自定义界面的 CSS 和 js。
**免费** 
你看 MDN 以及很多前端教程都推荐使用 VSCode，一方面也降低了学习难度不是。扩展生态强大。我仍然记得我第一次使用 gitlens 的惊艳，居然每一行代码都给我标出来了是最近一次是谁修改的，什么时候修改的，简直是甩锅利器。虽然我前面说了有相当一部分VSCode 的插件质量确实不行，但是优秀实用有趣的插件也不少。
**开源而且官方维护积极**
因为是个开源项目，不但可以通过其本身的代码设计学到很多东西，还可以清楚的看到当前的开发进度，每次更新都能给我惊喜。




