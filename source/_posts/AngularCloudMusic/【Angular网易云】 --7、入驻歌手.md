---
title: 【Angular网易云】--7、入驻歌手
tags: CloudMusic
categories: CloudMusic
abbrlink: 3c7973ed
date: 2020-11-01 17:23:38
---

# 入驻歌手
这节的效果是完成上页遗留的右侧歌手专辑名单的列表。
在服务里找到相关的API，这里有一点小问题，可能是API的版本更新问题，视频里老师的api 参数在新的里边并没有生效，所以就改了一点参数。
<!--more-->
****
# home.component.html

```html
<!--右侧开始-->
  <div class="right">
        <app-member-card></app-member-card>
        <div class="settled-singer">
          <div class="tit"><b>入驻歌手</b></div>
          <div class="list">
            <div class="card" *ngFor="let singer of singers">
              <div class="pic">
                <img [src]="singer.picUrl" [alt]="singer.name" />
              </div>
              <div class="txt">
                <b class="ellipsis">{{ singer.name }}</b>
                <span>专辑：{{ singer.albumSize }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
<!--右侧结束-->
```
****
# singer.service.ts
- 在service里新建singer服务
```typescript
// 定义传参类型。这里是用的新版的api，里边并没有找到cat的参数，而使用的area，并且取回的数据也并没有分页，可能是传的参数有问题，挖个坑
type SingerParams = {
  type?: number;
  area: number;
};
// 声明参数默认值
const defaultParams: SingerParams = {
  type: 1,
  area: 7,
};

  // 获取入驻歌手列表
  getEnterSingers(args: SingerParams = defaultParams): Observable<Singer[]> {
    const params = new HttpParams({ fromString: JSON.stringify(args) });
    console.log("params", params);
    return this.http
      .get(this.uri + "artist/list", { params })
      .pipe(map((res: { artists: Singer[] }) => res.artists));
  }

```
****
# member-card.component.html
由于右侧上方是一个快捷登录的卡片，所以会有状态的更新及数据的渲染，为了方便维护就新建一个组件member-card。
```html
<div class="member">
  <div class="login">
    <p>登录网易云音乐，可以享受无限收藏的乐趣，并且无限同步到手机</p>
    <button nz-button class="btn">用户登录</button>
  </div>
</div>
```

****

# member-card.component.less
```css
@import "../../../../../assets/styles/varibles";

.member {
  .login {
    height: 126px;
    background: url("../../../../../assets/images/index.png") no-repeat;
    p {
      width: 205px;
      margin: 0 auto;
      padding: 16px 0;
      line-height: 22px;
      font-size: @font-size-sm;
    }
    .btn {
      display: block;
      color: @white-color;
      width: 100px;
      height: 30px;
      margin: 0 auto;
      background: url("../../../../../assets/images/index.png") no-repeat 0 -195px;
      text-shadow: 0 1px 0 @wy-btn-red;
      border-color: @error-color;
    }
  }

  .n-myinfo {
    height: 184px;
    padding-top: 20px;
    background: url("../../../../../assets/images/index.png") no-repeat 0 -270px;
    .f-cb {
      .head {
        position: relative;
        float: left;
        display: block;
        width: 80px;
        height: 80px;
        margin-left: 20px;
        padding: 2px;
        background: @white-color;
        border: 1px solid #dadada;
      }

      .info {
        float: left;
        width: 115px;
        margin-left: 12px;
        .lv {
          margin: 5px 0 10px 0;
          .u-icn2 {
            display: inline-block;
            overflow: hidden;
            vertical-align: middle;
            background: url("../../../../../assets/images/icon2.png") no-repeat
              0 9999px;
          }
          .u-lv {
            height: 17px;
            padding-left: 25px;
            line-height: 18px;
            color: #999;
            font-weight: bold;
            font-style: italic;
            background-position: -130px -64px;
            .lvright {
              float: right;
              width: 8px;
              height: 17px;
              background-position: -192px -64px;
            }
          }
        }
      }
    }

    .dny {
      margin: 12px 0 0 20px;
      color: #666;
      li {
        float: left;
        height: 40px;
        padding: 0 18px;
        border-right: 1px solid #e4e4e4;
        &.fst {
          padding-left: 0;
        }
        &.lst {
          padding-right: 0;
          border-right: none;
        }
        strong {
          display: block;
          max-width: 40px;
          font-size: 20px;
          font-weight: normal;
        }
        span {
          margin-left: 2px;
        }
      }
    }
  }
}

```

****
# 效果
![](https://img-blog.csdnimg.cn/20201115221821224.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 总结
在调用api的时候，事先定义好来回的数据类型，可以起到优化的效果，减少内部判断类型。对于有状态更新的dom，要考虑是否要方便维护，从而决定是否单独提取出新的组件页。

