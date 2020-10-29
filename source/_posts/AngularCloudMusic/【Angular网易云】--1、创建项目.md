---
title: 【Angular网易云】--1、创建项目
tags: CloudMusic
categories: CloudMusic
abbrlink: '67862301'
date: 2020-10-29 11:01:36
description:
---

# 前言
目前对于angular的使用虽然有一段时间了，但是对于结构的认知依旧是很片面，一些关键的要点也是一知半解，所以一直想找一个全面介绍的视频或者课程。恰巧，今天刚刚看到一个大佬用**angular8+ngrx8**完成一项网易云音乐的管理项目，就想着在这位大佬的引领下补充自己未知的知识点，因为课程的内容时长很长，我也不确定什么时候能够跟完，但会一直督促自己，加油！
# 项目地址
- **B站视频**：https://www.bilibili.com/video/av70355308
- **GitHub**：https://github.com/lycHub/ng-wyy
<!--more-->
## API
该项目用了**[网易云音乐 NodeJS 版 API](https://binaryify.github.io/NeteaseCloudMusicApi/#/)**，所以该项目应该不太涉及后端的知识点，新建项目之前可以先安装网易云API。

- 先在[GitHub](https://github.com/Binaryify/NeteaseCloudMusicApi)上克隆项目
- 打开文件夹，在终端运行命令：**npm install**
- 运行：**node app.js**
![成功效果](https://img-blog.csdnimg.cn/20200914224325410.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
## 创建项目
按照大佬的步骤新建命令即可
>**npm i @angular/cli -g** // 安装cli
>**ng new  ng-wyy --style=less --routing -S** // 新建包含less和路由的项目
>	**ng add ng-zorro-antd** // 安装脚手架
![脚手架](https://img-blog.csdnimg.cn/20200914225255474.png#pic_center)

## 更改配置
因为主要介绍框架，所以并不涉及css样式的设计，大佬就选择了直接引用，包括网易云pc端的css，这个把在GitHub上克隆的项目中的文件夹复制覆盖就好![在这里插入图片描述](https://img-blog.csdnimg.cn/20200914225636306.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200914225558494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

## 今日总结
目前的进度只是再次复习创建项目的过程，但是熟能生巧，创建的过程中就感觉有些东西其实本可以省略的，还是不够熟练呀。就目前的进度来说，很慢，详细的看完三个视频并做笔记是很痛苦的过程，但是看到大佬演示最后的样子，是真的感觉Cow Beer，所以，不能中断呀！一定要坚持的跟下去，紧跟大佬脚步！