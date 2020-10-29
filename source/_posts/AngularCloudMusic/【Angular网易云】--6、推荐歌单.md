---
title: 【Angular网易云】--6、推荐歌单
tags: CloudMusic
categories: CloudMusic
abbrlink: 2f4fe59
date: 2020-10-29 11:08:12
description:
---

# 数据渲染
这次的内容是将调用后端接口并返回数据，将数据渲染到页面中，其中对返回的数据进行处理。

<!--more-->
****

# 调用接口
**home.service**
```typescript
  // 获取热门标签
  getHotTags(): Observable<HotTag[]> {
      return this.http.get(this.uri + "playlist/hot").pipe(
          map((res: { tags: HotTag[] }) => {
              return res.tags
          .sort((x: HotTag, y: HotTag) => {
              return x.position - y.position;
          })
          .slice(0, 5);
      })
    );
  }

  // 获取热门歌单
  getPersonalSheetList(): Observable<SongSheet[]> {
      return this.http
      .get(this.uri + "personalized")
      .pipe(map((res: { result: SongSheet[] }) => res.result.slice(0, 16)));
  }
```
在service中定义获取标签和歌单的方法。在两个方法中分别声明返回的类型(**HotTag[]、SongSheet[]**)


****

**common.types.ts**
```typescript
// 热门标签
export type HotTag = {
  id: number;
  name: string;
  position: number;
};
// 热门歌单
export type SongSheet = {
  id: number;
  name: string;
  picUrl: string;
  playCount: number;
};
```
再将返回回来的标签通过map()转换成(res: { tags: HotTag[] })格式。通过[sort]()将返回的数据根据position参数排序 **（sort中的形参为函数，根据对比两个参数的position大小来决定排序的顺序）**，在用[slice](https://www.w3school.com.cn/js/jsref_slice_array.asp)截取前五个即可
<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">


# 首页歌单和标签
**home.component.html(部分)**
```html
 <!--中间开始-->
  <div class="main">
    <div class="wrap">
      <!--左边开始-->
      <div class="left">
        <div class="sec">
          <!--热门标签开始-->
          <div class="up">
            <div class="navs">
              <h2>
                <i></i>
                <a>热门推荐</a>
              </h2>
              <nav *ngFor="let tag of hotTags">
                <a>{{ tag.name }}</a>
              </nav>
            </div>
            <a>
              更多
              <i nz-icon nzType="arrow-right" nzTheme="outline"></i>
            </a>
          </div>
          <!--热门标签结束-->

          <!--热门歌单开始-->
          <div class="down">
            <div class="down-wrap">
              <app-single-sheet
                class="sheet-item"
                *ngFor="let item of songSheet"
                [sheet]="item"
              ></app-single-sheet>
            </div>
          </div>
          <!--热门歌单结束-->
        </div>
      </div>
      <!--左边结束-->
      <div class="right"></div>
    </div>
  </div>
  <!--中间结束-->
```
其中的  <app-single-sheet> </ app-single-sheet> 标签是将歌单抽成单个的公用组件（**当系统中有很多页面有类似的组件，就可以抽出公用组件，导入并导出到公用模块中，这样其他地方使用只需导入公用模块即可**）。
****
**home.component.ts(部分)**
```typescript
	hotTags: HotTag[]; // 热门标签
  	songSheet: SongSheet[]; //热门歌单
  	
  ngOnInit() {
    this.getHotTags();
    this.getPersonalizedSheetList();
  }
  // 获取热门标签
  private getHotTags() {
    this.homeService.getHotTags().subscribe((tags) => {
      this.hotTags = tags;
    });
  }
  // 获取热门歌单
  private getPersonalizedSheetList() {
    this.homeService.getPersonalSheetList().subscribe((sheets) => {
      this.songSheet = sheets;
    });
  }
```
ts中直接调用服务中的接口即可，拿到返回值赋值给已声明的全局变量。
****


# 创建公共组件
在share目录下创建wy-ui目录及wy-ui模块以及一个过滤器play-count.pipe，在wy-ui目录下创建single-sheet组件(home组件使用)
>ng g m share/wy-ui
ng g c share/wy-ui/single-sheet
ng g p share/play-count

![效果图](https://img-blog.csdnimg.cn/20201009230810372.png#pic_center)
<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

**single-sheet.component.html**
```html
<ng-container>
  <a class="cover">
  <!--父组件传给子组件的值sheet:hotTag-->
    <img [src]="sheet.picUrl" [alt]="sheet.name" />
    <div class="bottom">
      <div class="num">
        <i class="icon erji"></i>
        <!--过滤器，歌单显示的听众数量为纯数字，要求将纯数字改为xx万，不超过一万则显示纯数字-->
        <span>{{ sheet.playCount | playCount }}</span>
      </div>
      <i class="icon play"></i>
    </div>
  </a>
  <span class="dec">{{ sheet.name }}</span>
</ng-container>

```
歌单的听众数量有要求，将超过一万的更改格式为xx万，所以就需要进行格式处理，创建一个过滤器并在share中导入导出，即可使用过滤器
****
**single-sheet.component.ts**
```typescript
export class SingleSheetComponent  {
// 接受父组件传值
  @Input() sheet: SongSheet;
}

```
****
**single-sheet.component.less（研究中）**
```css
@import "../../../../assets/styles/varibles";
@import "../../../../assets/styles/mixins";

.cover {
  position: relative;
  display: block;
  height: 140px;
  img {
    height: 100%;
  }
  .bottom {
    width: 100%;
    position: absolute;
    bottom: 0;
    background-color: @black-color;
    color: @white-color;
    opacity: 0.8;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 3px 8px;
    .num {
      span {
        margin-left: 8px;
        vertical-align: text-bottom;
      }
    }
    .icon {
      .icon("../images/iconall.png", 14px, 11px);
      &.erji {
        background-position: 0 -24px;
      }
      &.play {
        width: 16px;
        height: 17px;
        background-position: 0 0;
        &:hover {
          background-position: 0 -60px;
        }
      }
    }
  }
}
.dec {
  display: block;
  margin-top: 8px;
  cursor: pointer;
}

```
****
**play-count.pipe.ts**
```typescript
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({
  name: "playCount",
})
export class PlayCountPipe implements PipeTransform {
  transform(value: number): number | string {
    if (value > 10000) {
      return Math.floor(value / 10000) + "万";
    } else {
      return value;
    }
  }
}

```
这个过滤器只需要接受听众数量，所以value类型为number，但是可以返回纯数字和xx万类型，所以返回值就只能是number和string。根据math.floor()可以返回小于或等于一个给定数字的最大整数(向下取整)。
****
**效果图**
![](https://img-blog.csdnimg.cn/2020100923210813.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 总结
这节内容实现数据的渲染，在拿到返回数据时可以对数据进行二次处理，选取或者排序等等，在渲染过程也可以对数据进行处理，可以写通用的管道过滤器来处理数据的就结构。声明变量时最好也声明类型。