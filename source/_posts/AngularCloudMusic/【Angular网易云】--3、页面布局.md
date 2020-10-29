---
title: 【Angular网易云】--3、页面布局
tags: CloudMusic
categories: CloudMusic
abbrlink: f1e8bb7a
date: 2020-10-29 11:04:34
description:
---


# 根页面
创建完目录之后，就可以实现根页面了，各个子页面都是嵌套在根页面之中，所以根页面要有一定的布局。一般的pc页面都是包含头部、底部、中间的内容、左侧和右侧的菜单视情况而定。
![页面布局](https://img-blog.csdnimg.cn/20200917201656906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

<!--more-->
这个案例也是采用这种布局。![](https://img-blog.csdnimg.cn/20200917202213424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

# app.component

```html
<div id="app">
  <nz-layout class="layout">
  <!--头部-->
    <nz-header class="header">
      <div class="wrap">
        <div class="left">
          <h1>Music</h1>
          <ul nz-menu nzTheme="dark" nzMode="horizontal">
            <li nz-menu-item>发现</li>
            <li nz-menu-item>歌单</li>
          </ul>
        </div>
        <div class="right">
          <div class="search">
            <nz-input-group [nzSuffix]="suffixIconSearch">
              <input nz-input placeholder="歌单/歌手" />
            </nz-input-group>
            <ng-template #suffixIconSearch>
              <i nz-icon nzType="search"></i>
            </ng-template>
          </div>
          <div class="member">
            <div class="no-llogin">
              <ul nz-menu nzTheme="dark" nzMode="horizontal">
                <li nz-submenu>
                  <div title>
                    <span>登录</span>
                    <i nz-icon nzType="down" nzTheme="outline"></i>
                  </div>
                  <ul>
                    <li nz-menu-item>
                      <i nz-icon nzType="mobile" nzTheme="outline"></i>
                      手机登录
                    </li>
                    <li nz-menu-item>
                      <i nz-icon nzType="mobile" nzTheme="outline"></i>
                      注册
                    </li>
                  </ul>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </nz-header>
    <!--内容-->
    <nz-content class="content">
      <router-outlet></router-outlet>   <!--子页面-->
    </nz-content>
    <!--底部-->
    <nz-footer class="footer">
      Ant Design 02020 Immplement By Angular
    </nz-footer>
  </nz-layout>
</div>

```

## app.component.less

```css
#app .layout{
  .header {
    .wrap{
      display: flex;	//flex布局
      justify-content: space-between;	//对齐方式选择保持间隔
      width: 1100px;
      .left{
        display: flex;
        h1{
          width: 157px;
          color: @border-color-base;	
          font-size: @font-size-lgx;
          margin-bottom: 0;
          margin-right: 20px;
          text-align: center;
        }
        ul{
          height:64px;
          line-height:64px;
        }
      }
      .right{
        display: flex;
        .member{
          display: flex;
          align-items: center;
          color: @white-color;
        }
      }
    }
  }
  .content{
    background-color: @body-color;
  }
  .footer{
    text-align: center;
  }
}


:host ::ng-deep .ant-back-top {
  bottom: 80px;
}
```



<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

# 创建首页
- 在pages路径下执行创建命令
>**ng g m home**  // 创建模块
>**ng g c home**  // 创建组件

- 在home-routing-module中添加路由配置
>**const routes: Routes = [{ path: "home", component: HomeComponent }];** // 添加home页路由

- 在app-routing-module中添加路由配置
>// 全局配置，进入页面时，会重定向路由到home中
>**const routes: Routes = [{ path: "", redirectTo: "/home", pathMatch: "full" }];** 
<h3>运行之后</h3>

![](https://img-blog.csdnimg.cn/20200917205159982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 总结
　　网站页面布局中的内页模板有**两栏式**和 **三栏式**。 **两栏式多用于小型企业博客网站。两栏页面的中心部分是文章内容，侧面为用户互动内容，如推荐文章等**当然栏目页需要注意文章列表每页条目不要太少，会导致不便于用户浏览，也会出现网站页面层次过深的问题。**三栏的内页布局和两栏的差不多，中间位置为文章内容，两侧方式相应的互动内容或广告。**