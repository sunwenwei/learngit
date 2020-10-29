---
title: 【Angular网易云】--4、获取轮播图（一）
tags: CloudMusic
categories: CloudMusic
abbrlink: 6455a35d
date: 2020-10-29 11:06:00
description:
---


# 创建服务
在service路径下执行创建服务命令
>**ng g s home**

```javascript
@Injectable({
  providedIn: ServicesModule, // 哪一个模块提供的
})
```
其中HomeService中有一个**providedIn**属性，代表着**这个一个服务是谁提供的**；默认值‘root’代表是appModule提供的。但是这次结构是将子服务放进总服务中，所以providedIn值应改为ServicesModule。这样做的好处：**如果这测的服务没有被用到，就会被摇数优化给摇掉(tree shaking，一种编译优化的方式。可以自动清除你引用了但却未使用的模块或包，减小打包体积。)**

<!--more-->

# 服务实现
## home.service
```javascript
export class HomeService {
  constructor(
    private http: HttpClient,
    @Inject(API_CONFIG) private uri: string // 注入令牌
  ) {}

  // 获取轮播图
  getBanners(): Observable<Banner[]> {
    return this.http
      .get(this.uri + "banner")
      .pipe(map((res: { banners: Banner[] }) => res.banners)); //  一般不知道类型，可以使用any
  }
}
```
## service.module
```javascript
export const API_CONFIG = new InjectionToken("ApiconfigToken");

@NgModule({
  declarations: [],
  imports: [],
  providers: [{ provide: API_CONFIG, useValue: "http://localhost:3000/" }],
})
```
>
>**ApiconfigToken是什么意思？**
>**angular提供一个服务，都有一个令牌以及令牌的值，值可以是字符串或者类，令牌也可以直接写成字符串，但是可读性不好，一般写成key，value形式。**



<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

## home.component.ts
```javascript
export class HomeComponent implements OnInit {
  banners: Banner[]; //轮播图数据
  // 注入服务
  constructor(private homeService: HomeService) {}

  ngOnInit() {
  // 调用方法
    this.homeService.getBanners().subscribe((res) => {
      this.banners = res;
    });
  }
}
```
****

## home.component.html

```html
<div class="home">
  <div class="carousel wrap">
    <nz-carousel nzAutoPlay nzEffect="fade">
      <div
        class="carousel-item"
        nz-carousel-content
        *ngFor="let item of banners"
      >
        <a [href]="item.url" target="_blank" class="banner-item">
          <img [src]="item.imageUrl" />
        </a>
      </div>
    </nz-carousel>
  </div>
</div>
```
****

## home.component.less
```css
@import "../../../assets/styles/varibles";
@import "../../../assets/styles/mixins";
.home {
  .banner-item {
    display: block;
    width: 100%;
  }

  .main {
    .wrap {
      display: flex;
      justify-content: space-between;
      background-color: @white-color;
      border-left: 1px solid @border-color-base;
      border-right: 1px solid @border-color-base;
      .left {
        width: 726px;
        padding: 20px 20px 40px;
        .sec {
          .up {
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            padding: 0 10px 10px 10px;
            border-bottom: 2px solid @red-color;
            margin-bottom: 15px;
            .navs {
              display: flex;
              align-items: flex-end;
              h2 {
                font-size: @font-size-lgs;
                a:hover {
                  text-decoration: inherit;
                }
                i {
                  display: inline-block;
                  width: 15px;
                  height: 15px;
                  background-color: @white-color;
                  border: 3px solid @red-color;
                  border-radius: 50%;
                  margin-right: 10px;
                }
              }
              nav {
                a {
                  display: inline-block;
                  padding: 0 12px;
                  border-right: 1px solid @border-color-base;
                  //margin-left: 15px;
                  &:last-child {
                    border-right: none;
                  }
                }
              }
            }
          }

          .down {
            .down-wrap {
              display: flex;
              flex-wrap: wrap;
              .sheet-item {
                width: 140px;
                height: 204px;
                margin-right: 42px;
                &:nth-of-type(4n) {
                  margin-right: 0;
                }
              }
            }
          }
        }
      }
      .right {
        width: 250px;
        border-left: 1px solid @border-color-base;
        .settled-singer {
          padding: 15px;
          background-color: @white-color;
          .tit {
            font-size: @font-size-sm;
            overflow: hidden;
            padding-bottom: 8px;
            border-bottom: 1px solid @border-color-base;
            b {
              float: left;
            }
            span {
              float: right;
            }
          }

          .list {
            overflow: hidden;
            .card {
              margin-top: 14px;
              width: 210px;
              height: 62px;
              background: #fafafa;
              cursor: pointer;
              .pic {
                float: left;
                width: 62px;
                height: 62px;
              }
              .txt {
                float: left;
                border: 1px solid #e9e9e9;
                padding-left: 14px;
                width: 147px;
                height: 62px;
                b {
                  display: block;
                  margin-top: 8px;
                }
                span {
                  display: block;
                  margin-top: 8px;
                }
              }
            }
          }
        }
      }
    }
  }
}

```
****
# 最终实现
![实现](https://img-blog.csdnimg.cn/20200917222557327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
****
# 总结
关于服务的实现和注入并不是很复杂的地方，反而是装饰器有点陌生，一方面不知道是什么，另一方面就是不知道怎么用，为什么这么用；里边讲到了因域名重复写而要写一个token令牌，这并不是有很清晰的理解，用来保存后端交互的token认证信息和这个令牌有什么关联还有待学习，之前用到的Alain是新建了environment.ts来解决连接后端重复使用的域名，应该也是差不多的逻辑吧。