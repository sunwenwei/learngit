---
title: js实现倒计时
tags: [Example,js]
categories: Example
description: js实现倒计时--手动选择时间
date: 2020-10-11 17:23:38
abbrlink: 16107
---
 

# 前言
突然想到倒计时的案例，就试着写了一个，但是当时的思路是直接获取dom并输出，没有实现可复用性，而且耦合度很大，在搜索了几篇文章后，发现有个大佬写的确实很好，就摘抄一下，以加强理解。
****
# 效果图![效果图](https://img-blog.csdnimg.cn/20200914152141555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
这里可以实现以你选择的时间作为倒计时结束的时间
****
# countDown.html
```c
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>countDown</title>
    <link href="countdown.css" rel="stylesheet" type="text/css" />
    </link>
</head>

<body>
    <div id="select">
        <input type="datetime-local" id="selectTime" />
    </div>
    <div id="clear"></div>
    <div id="box">
        <h1>倒计时</h1>
        <div id="time">
            <strong><span id="day">**天</span></strong>
            <strong><span id="hour">**时</span></strong>
            <strong><span id="minute">**分</span></strong>
            <strong><span id="second">**秒</span></strong>
        </div>
    </div>
    <script src="countdown.js"></script>
    <script>
        var tempContent = my$('time');
        var daySpan = my$('day');
        var hourSpan = my$('hour');
        var minuteSpan = my$('minute');
        var secondSpan = my$('second');
        TimeRow();
        setInterval(TimeRow, 1000)
	// 这里是主要的显示回调
        function TimeRow() {
            var selectTime = my$('selectTime').value;
            var end = new Date(selectTime);
            var start = new Date();
            var time = getInterval(start, end);
            var tempDisable = end > start;
            if (tempDisable) {
                setInnerText(daySpan, time.day + '天');
                setInnerText(hourSpan, time.hour + '时');
                setInnerText(minuteSpan, time.minute + '分');
                setInnerText(secondSpan, time.second + '秒');
            } else {
                setInnerText(daySpan, '已');
                setInnerText(hourSpan, '超');
                setInnerText(minuteSpan, '时');
                setInnerText(secondSpan, '!');
            }
        }
    </script>
</body>

</html>

```

****


# countDown.css
```c
body {
    background-color: #2a3852;
}

#select {
    margin-top: 8px;
    float: right;
}

#box {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    margin-top: 300px;
}

#box h1 {
    letter-spacing: 3px;
    font-weight: 500;
    color: #fff;
}

#time {
    display: flex;
    flex-direction: row;
    line-height: 50px;
}

#time span {
    font-size: 20px;
    color: #fff;
}

#time strong {
    text-align: center;
    margin-left: 20px;
    background-color: #3f5174;
    border-radius: 10px;
    width: 70px;
    height: 50px;
    display: block;
}

#clear {
    clear: both;
}
```

****
# countDown.js

```c
function my$(id) {
    return document.getElementById(id);
}

// 处理innerText和textContent的兼容性问题
// 设置标签之间的内容
function setInnerText(element, content) {
    // 判断当前浏览器是否支持 innerText
    if (typeof element.innerText === 'string') {
        element.innerText = content;
    } else {
        element.textContent = content;
    }
}


function getInterval(start, end) {
    // 两个日期对象，相差的毫秒数
    var interval = end - start;
    // 求 相差的天数/小时数/分钟数/秒数
    var day, hour, minute, second;

    // 两个日期对象，相差的秒数
    // interval = interval / 1000;
    interval /= 1000;
	// 计算时间采取四舍五入
    day = Math.round(interval / 60 / 60 / 24);
    hour = Math.round(interval / 60 / 60 % 24);
    minute = Math.round(interval / 60 % 60);
    second = Math.round(interval % 60);

    return {
        day: day,
        hour: hour,
        minute: minute,
        second: second
    }
}
```
- 关于innerText和textContent的兼容性问题
>**IE支持innerHTML与innerText**
> **FireFox支持innerHTML与textContent**
>所以在IE与FireFox中使用显示内容时 必须得先判断浏览器 然后决定使用innerText还是textContent**
# 总结
这个大佬的代码很清晰，没有不必要的耦合，而不是单单的获取DOM树后在渲染，减少不必要的麻烦，对于时间起始和结束的判断以及超时显示是我后加的，想用三元表达式代替的，结果没写好，希望能够补充一下，不然赋值的那几行很是啰嗦。
>文章引用：https://segmentfault.com/a/1190000024433108