---
title: 【Angular2】--父子组件传值
tags:
  - Angular2
categories: Angular2
abbrlink: ab3048f6
date: 2020-10-12 16:29:40
---


# 父子组件之间传值


 ## 装饰器@Input与@Output

父组件引用子组件，两个组件内部都是一个输入框

**parent.component.html**

```html
<div style="border: darkred 1px solid; width: 50%;height: 50px; text-align: center;padding-top: 10px;">
  <label>parent：</label>
  <input nz-input #parentInput />
</div>
<div style=" text-align: center; ">
  <app-app-child [parentInput]="parentInput.value"></app-app-child>
</div>

```

<!--more-->
**child.component.html**
```html
<div style=" background-color: aqua; width: 50%;height: 50px; padding-top: 10px;">
  child：
  <input nz-input [placeholder]="parentInput" />
</div>

```
**child.component.ts**
```javascript
export class AppChildComponent implements OnInit {
  // 输入性装饰器，用来接收父组件传来的同名参数
  @Input() parentInput: string;

  constructor() {}

  ngOnInit(): void {}
}

```
**效果图**
在父组件内的输入框键入，就会传入子组件，父组件在引入的子组件上用[paramName]='value'传值， **<app-app-child [parentInput]="parentInput.value"></app-app-child>**  子组件用  **@Input() parentInput: string** 接收，
![效果图](https://img-blog.csdnimg.cn/20200922140624705.png#pic_center)
****


- ## 通过 setter 截听输入属性值的变化
[Angular官网介绍](https://angular.cn/guide/component-interaction#intercept-input-property-changes-with-a-setter)
在父组件内定义一个数组，将通过setter方式传入子组件
**parent.component.html**
```html
<div style="border: darkred 1px solid; width: 50%;height: 50px; text-align: center;padding-top: 10px;">
 parent：{{names}}
</div>
<div style=" text-align: center; ">
<!--ts中定义names数组，循环将name传入子组件中-->
  <app-app-child *ngFor="let name of names" [name]="name"></app-app-child>
</div>
```
**parent.component.ts**
```javascript
  names = ['Dr IQ', '   ', '  Bombasto  '];
```
**child.component.html**
```html
<div style=" background-color: aqua; width: 50%;height: 50px; padding-top: 10px;">
  child：{{name}}
</div>
```
**child.componetn.ts**
```javascript
export class AppChildComponent {
  private _name = ''; // 定义私有变量name，用来转换传入的数据

  @Input()
  // 当参数name被传入，就会调用set方法，来进行判断和赋值
  set name(name: string) {
    this._name = (name && name.trim()) || '<no name set>';
  }
// 当有地方使用name参数，就会调用get方法进行输出
  get name(): string {
    return this._name;
  }
}
```
**效果图**
子组件通过@Input接收父组件传入的数据，并通过自身设置的set,get方法进行判断并输出

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922152516522.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
****
## 通过ngOnChanges()来截听输入属性值的变化

- **child**
```javascript
import { Component, Input, OnChanges, SimpleChange } from '@angular/core';
@Component({
  selector: 'app-version-child',
  template: `
    <h3>Version {{major}}.{{minor}}</h3>
    <h4>Change log:</h4>
    <ul>
      <li *ngFor="let change of changeLog">{{change}}</li>
    </ul>
  `
})
export class VersionChildComponent implements OnChanges {
  @Input() major: number;
  @Input() minor: number;
  changeLog: string[] = [];

  ngOnChanges(changes: {[propKey: string]: SimpleChange}) {
    let log: string[] = [];
    for (let propName in changes) {
      let changedProp = changes[propName];
      let to = JSON.stringify(changedProp.currentValue);
      if (changedProp.isFirstChange()) {
        log.push(`Initial value of ${propName} set to ${to}`);
      } else {
        let from = JSON.stringify(changedProp.previousValue);
        log.push(`${propName} changed from ${from} to ${to}`);
      }
    }
    this.changeLog.push(log.join(', '));
  }
}
```
- **parent**
```javascript
import { Component } from '@angular/core';

@Component({
  selector: 'app-version-parent',
  template: `
    <h2>Source code version</h2>
    <button (click)="newMinor()">New minor version</button>
    <button (click)="newMajor()">New major version</button>
    <app-version-child [major]="major" [minor]="minor"></app-version-child>
  `
})
export class VersionParentComponent {
  major = 1;
  minor = 23;

  newMinor() {
    this.minor++;
  }

  newMajor() {
    this.major++;
    this.minor = 0;
  }
}
```
ngOnChanges是当数据绑定输入属性的值发生变化时而调用，我们父组件通过点击触发minor和major数值的变化，从而使子组件@Input接收的两个变量发生变化而调用ngOnChanges()函数。在子组件中 **for (let propName in changes) {}** 会被TSLint检测为异常，可以使用 **for (const propName of Object.keys(changes)) {}** 来代替。

****
## 父组件监听子组件的事件

**child.component.html**

```html
<div style=" background-color: aqua; width: 50%;height: 50px; padding-top: 10px;">
  child：
  <input nz-input #childInput />
  <!--子组件定义一个按钮，用来发射要传递的数据-->
  <button (click)="childSend(childInput.value)">child</button>
</div>

```
**child.component.ts**
```javascript
export class AppChildComponent implements OnInit {
  // 用 EventEmitter 和 output 装饰器配合使用 <string>指定类型变量
  @Output() childInput = new EventEmitter<string>();

  constructor() {}

  ngOnInit(): void {}
  // 子组件广播发射数据
  childSend(value: string) {
    this.childInput.emit(value);
  }
}
```
**parent.component.html**
```html
<div style="border: darkred 1px solid; width: 50%;height: 50px; text-align: center;padding-top: 10px;">
  <label>parent：</label>
  <input nz-input [placeholder]="placeholder" />
</div>
<div style=" text-align: center; ">
<!--用（子组件内output的参数名）="父组件接收事件函数" 来实现接收数据-->
  <app-app-child (childInput)="receive($event)"></app-app-child>
</div>

```
**parent.component.ts**
```javascript
export class AppParentComponent implements OnInit {
  constructor() {}
  
  placeholder = '';	// 父组件要显示的内容
  ngOnInit(): void {}
  //接收事件
  receive(value: string) {
    this.placeholder = value;
  }
}
```
**效果图**
![效果](https://img-blog.csdnimg.cn/20200922143416805.png#pic_center)

在子组件内使用@output装饰器和EventEmitter来装饰要传给父组件的参数及类型，通过子组件的点击事件触发广播发射数据，而父组件只需要定义接收数据事件即可。

****

## 父组件与子组件通过本地变量互动
**父组件不能使用数据绑定来读取子组件的属性或调用子组件的方法。但可以在父组件模板里，新建一个本地变量来代表子组件，然后利用这个变量来读取子组件的属性和调用子组件的方法**

**child.component.html**
```html
<!--简单的一个输入框-->
<div style=" background-color: aqua; width: 50%;height: 50px; padding-top: 10px;">
  child：
  <input nz-input [placeholder]="childValue" />
</div>

```
****
**child.componetn.ts**
```javascript
export class AppChildComponent {
  childValue = '123';	// 输入框显示的值
  // 变更显示
  changeValue() {
    this.childValue = '456';
  }
}
```
****
**parent.component.html**
```html
<div style="border: darkred 1px solid; width: 50%;height: 50px; text-align: center;padding-top: 10px;">
  <label>parent：</label>
  <input nz-input [placeholder]="child.childValue" />
  <button (click)="child.changeValue()">change</button>
</div>
<div style=" text-align: center; ">
  <app-app-child #child></app-app-child>
</div>

```
****
**效果图**
- 点击按钮前：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020092216402164.png#pic_center)
- 点击按钮后：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922164108482.png#pic_center)
由此可以看出，父组件只需要给子组件命名（#Name）即可用Name来调用子组件内的变量以及方法
****
## 父组件调用@ViewChild()
>本地变量方法是个简单便利的方法，但是它也有局限性，**因为父组件-子组件的连接必须全部在父组件的模板中进行。父组件本身的代码对子组件没有访问权**。如果**父组件的类需要读取子组件的属性值或调用子组件的方法，就不能使用本地变量方法。**
当父组件类需要这种访问时，可以把子组件作为 ViewChild，注入到父组件里面。

**child.component.html**
```html
<div style=" background-color: aqua; width: 50%;height: 50px; padding-top: 10px;">
  child：
  <input nz-input [placeholder]="childValue" />
</div>

```
****
**child.component.ts**
```javascript
export class AppChildComponent {
  @Output() childSend = new EventEmitter<string>();
  childValue = '123';

  changeValue(value: any) {
    value = '456';
    this.childSend.emit(value);
  }
}
```
****
**parent.component.html**
```html
<div style="border: darkred 1px solid; width: 50%;height: 50px; text-align: center;padding-top: 10px;">
  <label>parent：</label>
  <input nz-input [placeholder]="placeholder" />
  <button (click)="parentChangeValue()">change</button>
</div>
<div style=" text-align: center; ">
  <app-app-child (childSend)="childSend($event)"></app-app-child>
</div>

```
****
**parent.component.ts**
```javascript
export class AppParentComponent {
  @ViewChild(AppChildComponent) private child: AppChildComponent;
  placeholder = '123';

  parentChangeValue() {
    this.child.changeValue(this.placeholder);
  }
  childSend(value: string) {
    this.placeholder = value;
  }
}
```
****
**效果图**
- 点击前![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922171452735.png#pic_center)
- 点击后![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922171512697.png#pic_center)
**在这里，父组件直接将整个子组件注入，这样父组件类就能访问子组件的变量与类，当父组件点击change时，会调用父组件的parentChangeValue()函数，从而调用子组件的changeValue()函数，将参数变更，然后通过子组件将改变后的值发射出去，以便于父组件能够接到改变后的值，所以会在子组件上定义一个监听。**
****
## 父组件和子组件通过服务来通讯


>观察者（Observer）模式是一个软件设计模式，它有一个对象，称之为主体 Subject，负责维护一个依赖项（称之为观察者 Observer）的列表，并且在状态变化时自动通知它们。 该模式和发布/订阅模式非常相似（但不完全一样）。

>可观察对象是声明式的 —— 也就是说，虽然你定义了一个用于发布值的函数，但是在有消费者订阅它之前，这个函数并不会实际执行。 订阅之后，当这个函数执行完或取消订阅时，订阅者就会收到通知。

>可观察对象可以发送多个任意类型的值 —— 字面量、消息、事件。无论这些值是同步发送的还是异步发送的，接收这些值的 API 都是一样的。 由于准备（setup）和清场（teardown）的逻辑都是由可观察对象自己处理的，因此你的应用代码只管订阅并消费这些值就可以了，做完之后，取消订阅。无论这个流是击键流、HTTP 响应流还是定时器，对这些值进行监听和停止监听的接口都是一样的。

>一句话形容： **观察者只是具有三个回调的对象，用于处理Observable提供的每种类型的通知。**

作为发布者，你创建一个 Observable 的实例，其中定义了一个订阅者（subscriber）函数。 当有消费者调用 subscribe() 方法时，这个函数就会执行。 订阅者函数用于定义“如何获取或生成那些要发布的值或消息”。

要执行所创建的可观察对象，并开始从中接收通知，你就要调用它的 subscribe() 方法，并传入一个观察者（observer）。 这是一个 JavaScript 对象，它定义了你收到的这些消息的处理器（handler）。 subscribe() 调用会返回一个 Subscription 对象，该对象具有一个 unsubscribe() 方法。 当调用该方法时，你就会停止接收通知。


**parent.service**
```javascript
export class ParentService {
  // 数据源 next(发射数据)
  private parentToChildSource = new Subject<string>();

  // 数据流 subscribe(取得数据)，在组件中需要订阅
  parentToChild$ = this.parentToChildSource.asObservable();

  parentToChild(parentDataItem: string) {
    this.parentToChildSource.next(parentDataItem);
  }
}
```
****
**parent.component.html**
```html
<p>
  <button (click)="emissionDataToChild()">发射数据给子组件</button>
</p>
<ul>
  <li *ngFor="let data of dataFromChild">{{data}}</li>
</ul>
<!--
fieldset 元素可将表单内的相关元素分组。

<fieldset> 标签将表单内容的一部分打包，生成一组相关表单的字段。

当一组表单元素放到 <fieldset> 标签内时，浏览器会以特殊方式来显示它们，它们可能有特殊的边界、3D 效果，或者甚至可创建一个子表单来处理这些元素。

<fieldset> 标签没有必需的或唯一的属性。
-->
<fieldset>
  <legend>子组件部分：</legend>
  <app-app-child></app-app-child>
</fieldset>

```
****
**parent.component.ts**
```javascript
export class AppParentComponent {
  // 父组件数据
  parentData = ['来自父组件数据a', '来自父组件数据b', '来自父组件数据c'];

  // 存储来自子组件数据
  dataFromChild = [];
  nextData = 0;
  constructor(private parentService: ParentService) {}

  // 发射数据到子组件
  emissionDataToChild() {
    const toChildData = this.parentData[this.nextData++];
    this.parentService.parentToChild(toChildData);
    if (this.nextData >= this.parentData.length) {
      this.nextData = 0;
    }
  }
}
```
****
**child.component.html**
```html
<ul>
  <li *ngFor="let data of dataFromParent">{{data}}</li>
</ul>

```
****
**child.component.ts**
```javascript
export class AppChildComponent implements OnDestroy {

  // 存储来自父组件数据
  dataFromParent = [];
  subscription: Subscription;
  
  constructor(private parentService: ParentService) {
    this.subscription = parentService.parentToChild$.subscribe((data) => {
      this.dataFromParent.push(`${data}`);
    });
  }
  // 生命周期销毁钩子
  public ngOnDestroy() {
    this.subscription.unsubscribe();
  }
}
```
****
**效果图**
- 连续点击按钮四次
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200923102757411.png#pic_center)

****
>Subject是一个特殊的Observable,它允许将值多播给多个观察者。而且 每个 Subject 都是 Observable 每个 Subject 都是观察者 也就是说Subject可以被订阅,同时也具有观察者的三种方法:next,error,component
解释来源：[简书](https://www.jianshu.com/p/6a5bff3d38fd)

>试着理解一下：
>>1、当我点击父组件页面的按钮时，他就会触发父组件的emissionDataToChild()函数
>2、父组件会根据nextData的数值来确定parentData下标所代表的值
>3、接着，父组件会调用服务中的parentToChild(),并将要传给子组件的值作为参数传送过去
>4、服务内，已经声明Subject < string > 类型的parentToChildSource，会通过next()将值多播给已注册监听该 Subject 的观察者们
>5、其实这个时候传值就已经初步实现了，那只要规划好观察者就可以了，我们在让child作为观察者，**那么就需要观察一个可被观察的对象**，于是乎，我们在服务中将Subject类型的parentToChildSource通过asObservable()为一个可被观察的对象。
>6、在child中声明一个观察者（subscription: Subscription），让他来subscribe parentToChild$的变化，并把变化的值返回，再通过定义好的数组push进去就好了。

**Q**: Subscription是什么?
**A**:当subscribe一个observable的时候, 返回的就是一个subscription. 它是一个一次性对象(disposable), 它有一个非常重要的方法 ubsubscribe(), 它没有参数, 它会dispose掉subscription所持有的资源, 或者叫取消observable的执行.如果没有取消，就有可能会造成内存泄漏，这也是为什么要在生命周期销毁的时候调用取消订阅。

>服务通讯的理解是借助于[https://blog.csdn.net/Handsome_fan/article/details/84202758]()，对博主表示感谢。
# 总结
感觉父子组件传值比较常用的应该是装饰器和服务，模块化开发的话就会有一些非父子组件的传值，先留个坑，肝一肝再补上。