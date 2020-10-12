---
title: 【Angular2】--兄弟组件传值
tags:
  - Angular2
categories: Angular2
abbrlink: 162fe8c8
date: 2020-10-12 16:52:20
---


# 兄弟组件
- 兄弟组件一般使用中间人的方式实现交互，中间人也有不同的选择，可以使用第三者或者父组件也可以使用父服务，相对于第三者组件，父组件和服务的优点就体现出来了——**稳定** ， 父子组件一般是有耦合的，所以在确保模块的独立性时会选择父服务作为中间人。
****
<!--more-->


>本篇文章参照[angular8兄弟之间传值](https://blog.csdn.net/hl971115/article/details/102259417)
****

# 创建兄弟组件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200930135605728.png#pic_center)
相对于parent模块来说，tom 和 jack 是兄弟组件。parent.service就是选用的中间人。

****

# tom.component.html
```html
<div style="margin: 0; padding: 0;">
  <div
    style="width: 49%;height: 200px;text-align: center; border:1px solid red; margin-right: 5px;float:left ;padding: 5px;">
    <input [(ngModel)]="tom" />
    <button (click)="sendToJack()">tell parent</button>
  </div>
  <div
    style="width: 49%;height: 200px;text-align: center;  border:1px solid blue;margin-left: 5px; float:right;padding: 5px;">
    <app-jack></app-jack>
  </div>
  <div style="clear: both;"></div>
</div>

```

在tom组件内将jack组件引入，并调整样式。tom中有一个输入框，在tom中输入值后，点击按钮可将值传递到jack组件之中并显示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200930140102294.png#pic_center)


# tom.component.ts
```javascript
export class TomComponent  {
	// 注入服务
  constructor(private service: ParentService) {}
  // 输入框绑定值
  tom = '';
  // 发送值给jack组件
  sendToJack() {
    this.service.emit(this.tom);
  }
}
```
****
# parent.service.ts
>Subject是Observable的子类。- Subject是多播的，允许将值多播给多个观察者。普通的 Observable 是单播的。
>在 Subject 的内部，subscribe 不会调用发送值的新执行。它只是将给定的观察者注册到观察者列表中，类似于其他库或语言中的 addListener 的工作方式。
>要给 Subject 提供新值，只要调用 next(theValue)，它会将值多播给已注册监听该 Subject 的观察者们。

```javascript
export class ParentService {
  // 创建Subject事件
  private subject = new Subject();

  // 创建消费者监听
  listen = this.subject.asObservable();

  // 发送数据
  emit(value: any) {
    this.subject.next(value);
  }
}

```
在服务内，创建一个subject主题，并创建Observable监听者，在有监听者调用emit()方法时，就会将值发给监听者
****

# jack.component.ts
```javascript
export class JackComponent implements OnInit {
	// 注入服务
  constructor(private service: ParentService) {}
  // 兄弟组件传的值
  value = '';
  ngOnInit(): void {
  // 调用服务中的监听，若观察的数据有变化则会传给观察者们
    this.service.listen.subscribe((value: any) => {
      this.value = value;
    });
  }
}
```


<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

# 效果图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200930141258477.png#pic_center)
****

# 总结
[数星星等天明](https://blog.csdn.net/qq_34414916/article/details/85201246)
>我们使用Observable创建的可观察对象只能用于单播，也就是每次订阅就代表着不同的一次订阅者函数的执行，不同的观察者之间互不干扰，（不理解的话请继续阅读，后面会有具体的例子）

>而我们使用Subject创建的可观察对象是可以用于多播的，也就是多个观察者共享一个订阅者函数，生产者每次发送的值，都会发送给多播中的每一个观察者！

>Subject既可以作为可观察对象使用、又可以作为观察者使用！

>当Subject作为可观察对象使用的时候，其subscribe函数的作用变成了注册observer，而其next方法的作用变成了发送值