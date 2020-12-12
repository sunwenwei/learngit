---
title: 【Angular网易云】 --11、ngrx初始化
tags: CloudMusic
categories: CloudMusic
abbrlink: cc76f91f
date: 2020-11-26 17:23:38
---
# ngrx
##  **什么是ngrx**  

[ngrx官网](https://ngrx.io/docs)
ngrx是Angular基于Rxjs的状态管理，保存了Redux的核心概念，并使用RxJs扩展的Redux实现。使用Observable来简化监听事件和订阅等操作。

##  **为什么要用ngrx**


我们在开发中经常会碰到，这个界面的按钮需要在某种情况下变灰；那个界面上需要根据不同情况显示不同数量的Tab；这个界面的某个值的设定会影响另一个界面的某种展现等等。应该说应用开发中最复杂的部分就在于这些状态的管理。很多项目随着需求的迭代，代码规模逐渐扩大、团队人员水平参差不齐就会遇到各种状态管理极其混乱，导致代码的可维护性和扩展性降低。

> - Redux是为了解决应用程序状态（State）管理而提出的一种解决方案。对于应用开发来讲，UI上显示的数据、控件状态、登陆状态、数据加载画面的不同状态等等全部可以看作状态。
>- Redux 的三个概念：Reducer、Action、Store
>- Store 一般负责：保存应用状态、提供访问状态的方法、派发Action的方法以及对于状态订阅者的注册和取消等。(可以理解成内存数据库)
>- Reducer 其实就是用来维护状态的。reduce就是对数组元素进行累加计算成为一个值。（可以理解成数据库表，但这种说法不太精确）
>- Action 在Redux规范中，所有的会引发状态更新的交互行为都必须通过一个显性定义的Action来进行。(Reducer和Store之间的通信靠Action)
>

<!--more-->
- 示意图
![](https://img-blog.csdnimg.cn/2020120514260743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20201205142511134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70)

****

# 步骤
安装ngrx，使用ng add @ngrx/store创建安装命令，在用ng add @ngrx/store-devtools来安装环境测试工具

根据所需要的文件来创建相应的文件，在Store文件夹下创建actions、reducers和selectors文件夹。

- player.reducer.ts
```typescript
// 设置动作
export const SetPlaying = createAction('[player] set playing', props<{ playing: boolean }>());
export const SetPlayList = createAction('[player] set playList', props<{ playList: Song[] }>());
export const SetSongList = createAction('[player] set songList', props<{ songList: Song[] }>());
export const SetPlayMode = createAction('[player] set playMode', props<{ playMode: PlayMode }>());
export const SetCurrentIndex = createAction('[player] set currentIndex', props<{ currentIndex: number }>());

```
- player.reducer.ts
```typescript
export type PlayState = {
    playing: boolean; // 播放状态
    playMode: PlayMode; // 播放模式
    songList: Song[]; // 歌曲列表
    playList: Song[]; // 播放列表
    currentIndex: number // 当前正在播放的索引
}

// 初始数据格式
export const initialState: PlayState = {
    playing: false,
    songList: [],
    playList: [],
    playMode: { type: 'loop', label: '循环' },
    currentIndex: -1,
}
// 注册动作
const reducer = createReducer(
    initialState,
    on(SetPlaying, (state, { playing }) => ({ ...state, playing })),
    on(SetPlayList, (state, { playList }) => ({ ...state, playList })),
    on(SetSongList, (state, { songList }) => ({ ...state, songList })),
    on(SetPlayMode, (state, { playMode }) => ({ ...state, playMode })),
    on(SetCurrentIndex, (state, { currentIndex }) => ({ ...state, currentIndex })),
)
export function playerReducer (state: PlayState, action: Action) {
    return reducer(state, action)
}
```
- player.selector.ts
```typescript
const selectPlayerStates = (state: PlayState) => state;
// 声明各个动作
export const getPlaying = createSelector(selectPlayerStates, (state: PlayState) => state.playing)
export const getPlayList = createSelector(selectPlayerStates, (state: PlayState) => state.playList)
export const getSongList = createSelector(selectPlayerStates, (state: PlayState) => state.songList)
export const getPlayMode = createSelector(selectPlayerStates, (state: PlayState) => state.playMode)
export const getCurrentIndex = createSelector(selectPlayerStates, (state: PlayState) => state.currentIndex)
export const getCurrentSong = createSelector(selectPlayerStates, ({ playList, currentIndex }: PlayState) => playList[currentIndex])
```
在Store下定义配置文件index.ts，其中导入StoreModule
- index.ts
```javascript
@NgModule({
    declarations: [],
    imports: [
        StoreModule.forRoot({ player: playerReducer }, {
            runtimeChecks: {
                strictActionImmutability: true,
                strictActionSerializability: true,
                strictStateSerializability: true,
                strictStateImmutability: true,
            }
        }),
        // 调试插件
        StoreDevtoolsModule.instrument({
            maxAge: 20,
            logOnly: environment.production, // 生产环境下仅打印log
        })
    ]
})
```
在配置模块下注入StoreModule，定义启动reduce，在actions中定义动作，receder中注册使用。
****

# 总结

>[不苦先生](https://www.cnblogs.com/tarena/p/8473961.html)
>[接灰的电子产品](https://www.jianshu.com/p/0deec21d728f)
>[霞霞的博客](https://blog.csdn.net/weixin_42227767/article/details/104562135)
感谢以上大佬的博客，点击可看原文。