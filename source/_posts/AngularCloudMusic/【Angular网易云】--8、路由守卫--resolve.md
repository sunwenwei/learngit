---
title: 【Angular实战网易云】——8、路由守卫--resolve
tags: CloudMusic
categories: CloudMusic
abbrlink: c3084363
date: 2020-11-08 17:23:38
---
# 路由守卫
## 什么是路由守卫
 字面理解，路由守卫就是保护路由的跳转。那为什么需要保护路由呢？实际场景中，会有很多不同的访问路径，分别控制不同的场景，就需要路由守卫。
 > - 该用户可能无权导航到目标组件。
> - 可能用户得先登录（认证）。
> - 在显示目标组件前，你可能得先获取某些数据。
> - 在离开组件前，你可能要先保存修改。
> - 你可能要询问用户：你是否要放弃本次更改，而不用保存它们？

而路由器也是可以应对这些不同的场景
> - 用CanActivate来处理导航到某路由的情况。
> - 用CanActivateChild来处理导航到某子路由的情况。
> - 用CanDeactivate来处理从当前路由离开的情况.
> - 用Resolve在路由激活之前获取路由数据。
> - 用CanLoad来处理异步导航到某特性模块的情况。

<!--more-->
****
# resolve 


这一节是使用了resolve预先获取组件数据
>- 如果你在使用真实 api，很有可能数据返回有延迟，导致无法即时显示。 在这种情况下，直到数据到达前，显示一个空的组件不是最好的用户体验。
>- 最好使用解析器预先从服务器上获取完数据，这样在路由激活的那一刻数据就准备好了。 还要在路由到此组件之前处理好错误。 但当某个 id 无法对应到一个危机详情时，就没办法处理它。 这时最好把用户带回到“危机列表”中，那里显示了所有有效的“危机”。
>- 总之，你希望的是只有当所有必要数据都已经拿到之后，才渲染这个路由组件。
****

# 步骤
- 创建**home-resolve.service.ts**
> 在home路径下 ： **ng g s home-resolve**

- home-resolve.service.ts
```typescript
import { Injectable } from '@angular/core';
import { Resolve } from '@angular/router';
import { forkJoin, Observable } from 'rxjs';
import { first } from 'rxjs/internal/operators';
import { Banner, HotTag, Singer, SongSheet } from 'src/app/services/data-types/common.types';
import { HomeService } from 'src/app/services/home.service';
import { SingerService } from 'src/app/services/singer.service';

// 定义首页返回数据类型
type HomeDataType = [Banner[],HotTag[],SongSheet[],Singer[]]; 

@Injectable()
export class HomeResolverService implements Resolve<HomeDataType> {
  constructor(
    private homeService: HomeService,
    private SingerServe: SingerService
  ) {}

  resolve( ): Observable<HomeDataType> {
   return forkJoin([
       this.homeService.getBanners(),
       this.homeService.getHotTags(),
       this.homeService.getPersonalSheetList(),
       this.SingerServe.getEnterSingers(),
    ]).pipe(first());
  }
}
```
> - 在angular项目中使用rxjs的observable来控制异步请求很方便很舒服，但是有些时候得考虑一些特殊的问题，比如有两个请求相互依赖的情况，希望在所有请求都响应后再采取行动，如何处理？
> - 以前使用Promise的时候，有个promise.all的方法，可以控制所有请求请求完成后执行操作，相同的，rxjs也提供了forkJoin操作符来控制请求的并行。
> - forkJoin操作符接受一组observable作为参数，当所有的observable完成时，将每个observable的最新值作为数组发出。

[RxJS常用方法](https://blog.csdn.net/weixin_43964866/article/details/109541288)
![](https://img-blog.csdnimg.cn/20201117192524355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
****

- 写好调用之后怎么样才能使用呢 ？ 在routing中定义如下
> 
> // 这样在连通路由之前就会先走向resolve指向的HomeResolverService
> const routes: Routes = [
    { path: "home", component: HomeComponent, resolve: {homeDatas: HomeResolverService} }
];

相应的，home.comonent.ts中就不用再去调用api，而是调用resolve，使用resolve返回来的数据就可以了。
```typescript
  constructor(
    private route: ActivatedRoute
  ) {}
 ngOnInit() {
      this.route.data.pipe(map(res => res.homeDatas)).subscribe(res => {
          console.log('res:', res);
          this.banners = res[0];
          this.hotTags = res[1];
          this.songSheet = res[2];
          this.singers = res[3].slice(0, 9);
      })
  }

```

# 总结
路由守卫是一个难点，目前还只是稍微接触了一个小点，之前写项目时，并没有使用这个，所以在当时就有很多关于调用的顺序和数据的渲染之类的疑惑，记得当时用的promise，其实都能实现，但这个我理解的好处就是解耦的更好一点，ts中没必要在初次渲染时长篇大论。
