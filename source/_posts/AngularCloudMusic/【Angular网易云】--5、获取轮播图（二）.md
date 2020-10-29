---
title: 【Angular网易云】--5、获取轮播图（二）
tags: CloudMusic
categories: CloudMusic
abbrlink: 55f6b298
date: 2020-10-29 11:07:09
description:
---


# 组件封装
在home路径下新建component文件夹，在component文件夹下新建wy-carousel。这一层是用来封装轮播图两侧及底部动作实现的组件。
<!--more-->
## carousel.component.less
```css
.carousel {
  padding: 10px 0;
  background-color: #001529;
  .wrap {
    position: relative;
    .dot {
      display: block;
      width: 20px;
      height: 20px;
      overflow: hidden;
      background: url("./banner.png") 3px -343px;
      cursor: pointer;
      &.active {
        background-position: -16px -343px;
      }
    }
    .arrow {
      display: block;
      position: absolute;
      width: 37px;
      top: 50%;
      margin-top: -20px;
      color: #fff;
      font-size: 40px;
      cursor: pointer;
      &.left {
        left: -68px;
      }
      &.right {
        right: -68px;
      }
    }
  }
}

```
## carousel.component.html
```html
<div class="carousel">
  <div class="wrap">
  <!--左侧箭头，用来切换轮播图-->
    <i
      nz-icon
      class="arrow left"
      nzType="left"
      nzTheme="outline"
      (click)="onChangeSlide('pre')"
    ></i> 
    <ng-content> </ng-content>
  <!--底部圆点，覆盖原有的指示器-->
  <!--Zorro已经把索引返回出去了，用let-something 来接收索引-->
    <ng-template #dot let-number>
 		 <!--获取底部当前的下标，以便添加高亮样式-->
      <i class="dot" [class.active]="activeIndex === number"></i>
    </ng-template>
  <!--右侧箭头，用来切换轮播图-->
    <i
      nz-icon
      class="arrow right"
      nzType="right"
      nzTheme="outline"
      (click)="onChangeSlide('next')"
    ></i>
  </div>
</div>

```

<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

##  carousel.component.ts
这里涉及到了组件之间的传值，而home和carousel之间复合父子组件，所以使用了注解的方式。
```javascript
// carousel是home的子组件，而父子组件传值可以用注解的方式
// angular8要求在朱姐@viewChild时必须带上｛static: bool｝，以表示该组件是否是动态的
// dotRef是父组件要用的值，而dotRef是子组件中的dom
  @ViewChild("dot", { static: true }) dotRef: TemplateRef<any>;
  // 指示器模板已经被父组件home使用，拿到activeIndex(指示器下标索引)就要靠父组件传值，所以使用@input（）
  @Input() activeIndex = 0;
  // 将点击类型发射到父组件，以便于控制轮播图
  // 这里本可以将EventEmitter定义为<any>类型，但是为了严禁性，EventEmitter发射的类型只能是<"pre" | "next">
  @Output() changeslide = new EventEmitter<"pre" | "next">();
  constructor() {}

  ngOnInit() {}
  
  // 点击之后需要把类型发射到父组件中，才能控制轮播图,使用emit()
  onChangeSlide(type: "pre" | "next") {
    this.changeslide.emit(type);
  }
```
****

## home.component.html
```html
<div class="home">
<!--使用封装的两侧组件-->
<!--[activeIndex]="carouselActiveIndex": 子组件需要传入activeIndex，所以加上[]，而carouselActiveIndex表示当前父组件拿到的下标索引返回值，父子传值的方式-->
<!--子组件发射一个事件，父组件使用同名参数(changeslide)来监听	-->
  <app-wy-carousel
    #wyCarousel
    [activeIndex]="carouselActiveIndex"
    (changeslide)="onChangeSlide($event)"
  >
	<!--中间轮播图的内容区域，使用了carousel组件中的ng-content-->
      <!--nzDotRender: 使用子组件的Dot渲染模板-->
      <!--nzBeforeChange: 切换前的回调，有两个参数{ from: number; to: number }，
      将‘to’的参数传回到子组件内，以便可以判断下标并进行高亮-->
    <nz-carousel
      nzAutoPlay
      nzEffect="fade"
      [nzDotRender]="wyCarousel.dotRef"  
      (nzBeforeChange)="OnBeforeChange($event)" 
    >
    <!--(nzBeforeChange)="OnBeforeChange($event)" : 父组件获取轮播回调的数据-->
      <div
        class="carousel-item"
        nz-carousel-content
        *ngFor="let item of banners"
      >
        <a [href]="item.url" target="_blank" class="banner-item">
          <img [src]="item.imageUrl" />
        </a>
      </div>
	<!--轮播图结束-->
    </nz-carousel>
<!--两侧组件结束-->
  </app-wy-carousel>
</div>

```
****
## home.omponent.ts

```javascript
@Component({
  selector: "app-home",
  templateUrl: "./home.component.html",
  styleUrls: ["./home.component.less"],
  // angular是组件树构成的项目，他的变更监测默认是一个组件发生改变的时候，会把组件树上关联的子组件和父组件全部变更监测一遍，检测有无变化。OnPush策略只会在input()输入属性发生变化之后才会变更监测，否则其他组件发生变化的时候不影响到他的输入属性，那他就不会变更监测，有利于提升性能
  // 理想状态下，在你完全知道每个组件什么时候会发生变更时，angular每个组件都用上OnPush策略会提升不少的性能
  changeDetection: ChangeDetectionStrategy.OnPush,
})
 banners: Banner[];
  carouselActiveIndex = 0;
  // 拿到轮播组件的实例，这样就能调用轮播组件的切图方法
  @ViewChild(NzCarouselComponent, { static: true })
  private nzcarousel: NzCarouselComponent;

  constructor(private homeService: HomeService) {}

  ngOnInit() {
    this.homeService.getBanners().subscribe((res) => {
      this.banners = res;
    });
  }
  // 切换前的回调，用来接收组件返回的下标，，{to}表示只接受to参数
  OnBeforeChange({ to }) {
    this.carouselActiveIndex = to;
  }
  onChangeSlide(type: "pre" | "next") {
    this.nzcarousel[type]();
  }
 
```
**轮播组件的切换方法**
![](https://img-blog.csdnimg.cn/2020092123044958.png#pic_center)
****
## 效果图
**两侧的箭头、底部指示器的圆点以及圆点红色的高亮**
![](https://img-blog.csdnimg.cn/20200921232745318.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

****
# 总结
对于这一节的理解，我都尽量表述出来以加强自己的记忆，也发现了自己的短板——组件之间的传值，关于这次父子组件之间的传值，并不是很熟悉，会想着用一两天好好研磨组件之间的传值并写一篇有关自己的理解。