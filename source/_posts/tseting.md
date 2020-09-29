---
title: The frist Blog of testing
tags: '-testing'
categories: Testing
description: 超简洁后台系统静态登陆页面
abbrlink: 44873
---
# 静态登录页面

今天在网上看到一个后台登录页面，很简洁，看着也很舒服，就试着模仿做一下这个小案例(这个案例的水平很低，只是我复习css的第一步)。没有验证码和js逻辑，就只是简单地一个页面渲染。我感觉一般的后台管理登录页面应该不会注重样式(可能觉得没必要，只要能用就行)。这个小案例也算是我重拾css的第一步，样式用惯了框架，真要写的时候才发现都给忘了。。。

## 效果图![效果图](https://img-blog.csdnimg.cn/2020091320435635.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

****
## login.html
```javascript
<!DOCTYPE html>
<html lang="en">

<head>
  <mate charset="UTF-8">
    <title>login</title>
    <link rel="stylesheet" type="text/css" href="login.css">
</head>

<body>
  <div class="login-user">
    <div class="login-btn">
      <h1>login</h1>
      <div class="user">
        <input type="text" placeholder="userName">
      </div>
      <div class="pass">
        <input type="text" placeholder="passWord">
      </div>
      <button>登录</button>
    </div>
  </div>
</body>

</html>
```

****
## login.css
```javascript
// 重设边距和填充
* {
  margin: 0;
  padding: 0;
}
// 添加背景和比例
body {
  background: url("background.jpg");
  background-size: 100%;
}

.login-user {
  margin: auto;
  width: 400px;
  height: 300px;
  text-align: center;
  margin-top: 200px;
  background: rgb(0, 0, 0, 0.5);
  border-radius: 1em;
  transition: 0.3s;
}

.login-user:hover {
  box-shadow: 0 0 3px 3px rgba(0, 0, 0, 0.4);
}

.login-btn h1 {
  font-size: 60px;
  margin: 10px;
  color: #fff;
}

.login-user>div input {
  width: 200px;
  height: 35px;
  margin: 15px;
  background-color: transparent;
  border: 0;
  outline: none;
  border-bottom: 2px solid #fff;
  font-size: 20px;
  color: #fff;
}

.login-user>div button {
  width: 150px;
  height: 35px;
  background-color: transparent;
  border: 0;
  outline: none;
  border-radius: 1em;
  margin-top: 10px;
  border: 1px solid #fff;

}
```
## Q&A
- **为什么要先重设边距和填充？**
	1、默认的块元素有些会自带margin或者padding。
2、body默认就会有8个像素margin，这样设定后可以清除body的margin。
3、清除这些元素的默认margin和padding后我们自定义的css样式才不会有偏差。
4、同时设置margin为0也有为了居中对其的目的。
（来源于：https://www.cnblogs.com/H-Gfeng/p/10422365.html）


## 总结
对于像我这样的菜鸟来说，再回首从学校出来后带出来的css知识，已经远远地跟不上同时期的同学了，目前对于系统的前端知识理解还未入门，但是我相信，只要肯学，我就会有进步，加油！！！