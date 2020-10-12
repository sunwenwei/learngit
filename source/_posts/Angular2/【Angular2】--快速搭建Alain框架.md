---
title: 【Angular2】--快速搭建Alain框架
tags: Angular2
categories: Angular2
abbrlink: b758f302
date: 2020-10-12 16:10:26
description:
---

# Angular搭建模板框架
**前言**：网络不好，可以使用国内淘宝镜像。本模板的脚手架使用[**Ng Alain**](https://ng-alain.com/docs/getting-started/zh)，本地环境需要安装 node 和 git。技术栈基于 [Typescript](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)、[Angular](https://angular.cn/docs)、[g2](https://antv.vision/zh#products)、@delon 和 [ng-zorro-antd](https://ng.ant.design/docs/introduce/zh)。
<!--more-->
- 配置国内镜像：
> 打开终端，输入 **npm config get registry** 来查看当前地址

>更改地址：**npm config set registry https://registry.npm.taobao.org**
## 安装（推荐CLI）
>创建项目：**ng new my-project --style less --routing**
打开项目：**cd my-project**
安装脚手架：**ng add ng-alain**
运行：**npm start**
## 在安装脚手架时的提示![alain](https://img-blog.csdnimg.cn/20200910104713684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

- **你愿意在谷歌的隐私政策下，与谷歌Angular团队分享这个项目的 匿名使用数据吗？**
- **选择你想使用的默认语言**
- **是否添加hmr插件**
- **是否添加代码样式插件**
- **是否添加动态模具（sf组件）插件**
- **是否添加模拟数据插件**
- **是否添加国际化插件**

## 安装完成![alain](https://img-blog.csdnimg.cn/20200910110008127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
- 账号输入**admin**/**user**
- 密码输入**ng-alain.com**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020091011024826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

## 目录结构
>**NG-ALAIN 是一个标准的 Angular CLI 构建的项目，并提供了涵盖中后台开发的各类功能和坑位，下面是整个项目的目录结构。**
>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200910111307628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
