---
title: 【Angular网易云】--2、构建项目
tags: CloudMusic
categories: CloudMusic
abbrlink: a93691ee
date: 2020-10-29 11:03:30
description:
---


# 构建目录
使用过angular，就会知道什么是模块化，顾名思义，就是把整体分割成能够单独运行的一个各模块，这样做的好处有很多，比如**实现逻辑更清晰、可读性强**、**团队开发分工明确,容易控制**、**充分利用可以重用代码**、**抽象出可公用的模块,可维护性强**、**模块化的遗留系统方便组装开发新的相似系统**等等。angular都有一个根模块用于调用整个项目，但是所有引用都放在根模块会对项目产生一定的代价，所以构建一个比较合理的目录，创建子模块来分担跟根模块的负载是很重要的。
<!--more-->
# 一、创建文件夹
在app路径下执行以下代码来创建模块
>**ng g m core**    
>**ng g m share**
>**ng g m pages**
>**ng g m services**

创建完成之后就会有这样的结构：

![目录](https://img-blog.csdnimg.cn/2020091621333439.png#pic_center)

# 二、功能模块
## app.modult.ts
```javascript
@NgModule({
  declarations: [AppComponent],
  imports: [CoreModule],
  bootstrap: [AppComponent],
})
export class AppModule {}
```
****
## core.module.ts

```javascript
@NgModule({
  declarations: [],
  imports: [
    BrowserModule,
    HttpClientModule,
    BrowserAnimationsModule,
    ServicesModule,
    PagesModule,
    ShareModule,
    AppRoutingModule,
  ],
  exports: [ShareModule, AppRoutingModule],

  providers: [{ provide: NZ_I18N, useValue: zh_CN }],
})
export class CoreModule {
  constructor(@SkipSelf() @Optional() parentModule: CoreModule) {
    if (parentModule) {
      throw new Error("CoreModule只能被APPmodule引入");
    }
  }
}
```
****
## pages.modult.ts
```javascript
@NgModule({
  declarations: [],
  imports: [ShareModule],
})
export class PagesModule {}

```
****
## services.modult.ts
```javascript
@NgModule({
  declarations: [],
  imports: [],
})
export class ServicesModule {}

```
****
## share.modult.ts
```javascript
@NgModule({
  declarations: [],
  imports: [NgZorroAntdModule, FormsModule, CommonModule],
  exports: [NgZorroAntdModule, FormsModule, CommonModule],
})
export class ShareModule {}

```
****
**为什么coreModule里会有一层逻辑？**
大佬是这么解释的：
>在这里，coreModule相当于根模块，但是作为子模块，它只能被appModule引用，为了避免被其他功能模块引用，所以会有一层判断，如果其他模块引用了他，那么就会抛出一个错误。所以需要注入他本身作为父模块，但是第一次读取的时候呢，coreModule是找不到的，所以就会陷入死循环中，所以加一个 **@SkipSelf**装饰器修饰这个参数用来跳过本身；并且允许coreMOdule是可以不存在的，所以可以再加一个 **@Optional**装饰器，这样如果找不到coreModule就会赋值null，而不会抛出错误。



# 三、总结
很绕，这一节视频足足看了三四遍，也没明白为什么会有一层拦截，先埋个坑！
关于模块，最好的理解就是能够分离开发，做到互不干扰，在一个功能修改的时候尽量不去改变其他模块的代码，就大大减少了耦合度。
