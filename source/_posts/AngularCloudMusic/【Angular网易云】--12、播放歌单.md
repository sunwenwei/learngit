---
title: 【Angular网易云】--12、播放歌单
tags: CloudMusic
categories: CloudMusic
abbrlink: 2ac17ac2
date: 2020-12-02 17:23:38
---


# 逻辑过程
在初始化ngrx之后，就要考虑如何使用，事件的场景是点击歌单之后会播放歌曲，所以要声明一个播放事件。

- home.component.html
```html
    <!--热门歌单开始-->
          <div class="down">
            <div class="down-wrap">
              <app-single-sheet
                class="sheet-item"
                *ngFor="let item of songSheetList"
                [sheet]="item"
                (onPlay)="onPlaySheet($event)"
              ></app-single-sheet>
            </div>
          </div>
          <!--热门歌单结束-->
```
在点击之后，触发onPlaySheet（）事件
<!--more-->

- home.component.ts
```typescript
    constructor(
        private route: ActivatedRoute,
        private sheetServe: SheetService,
        private store$: Store<AppStoreModule>,
    ) {
        this.route.data.pipe(map(res => res.homeDatas)).subscribe(([banners, hotTags, songSheetList, singers]) => {

            this.banners = banners;
            this.hotTags = hotTags;
            this.songSheetList = songSheetList;
            this.singers = singers.slice(0, 9);
        })
    }

   
    // 点击歌单，获取歌单信息
    onPlaySheet (id: number) {
        this.sheetServe.playSheet(id).subscribe((list) => {
            this.store$.dispatch(SetSongList({ songList: list }));
            this.store$.dispatch(SetPlayList({ playList: list }));
            this.store$.dispatch(SetCurrentIndex({ currentIndex: 0 }));
        })
    }
```
通过sheetService中的playSheet方法将拼装好URL的歌曲列表通过player.action.ts中设置的动作，来获取当前列表中下表为0的歌曲。

此时player.selector.ts中的函数值就会发生改变，就会触发订阅。所以在wy-player模块就会被触发。

- wy-player.component.html
```html
    <audio #audio [src]="currentSong?.url" (canplay)="onCanPlay()"></audio>
```
- wy-player.component.ts
```typescript

    songList: Song[];
    playList: Song[];
    currentIndex: number;
    currentSong: Song;

    @ViewChild('audio', { static: true }) private audio: ElementRef;
    private audioEl: HTMLAudioElement;


    constructor(
        private store$: Store<AppStoreModule>
    ) {
        const appStore$ = this.store$.pipe(select(getPlayer));

        appStore$.pipe(select(getSongList)).subscribe(list => this.watchList(list, 'songList'));
        appStore$.pipe(select(getPlayList)).subscribe(list => this.watchList(list, 'playerList'));
        appStore$.pipe(select(getCurrentIndex)).subscribe(index => this.watchCurrentIndex(index));
        appStore$.pipe(select(getPlayMode)).subscribe(mode => this.watchPlayMode(mode))
        appStore$.pipe(select(getCurrentSong)).subscribe(song => this.watchCurrentSong(song));
    }

    ngOnInit () {
    // 另存选中的audioDOM
        this.audioEl = this.audio.nativeElement;
    }

	// 监听列表，type用来标识song 和 player
    private watchList (list: Song[], type: string) {
        this[type] = list;
    }
	// 监听当前歌曲下标
    private watchCurrentIndex (index: number) {
        this.currentIndex = index;
    }
	// 监听播放模式
    private watchPlayMode (mode: PlayMode) {
        console.log('mode', mode);
    }
	// 监听当前歌曲
    private watchCurrentSong (song: Song) {
        this.currentSong = song;
        console.log('song', song);
    }
	// 触发播放
    onCanPlay () {
        this.play();
    }
	// 播放回调
    private play () {
        this.audioEl.play();
    }

```
其中有点不同的是ngrx中的select方法，因为版本的原因而参数不能使用字符串，所以需要另外声明一个变量。
![](https://img-blog.csdnimg.cn/20201212120602961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70)


- player.selector.ts
```typescript

const selectPlayerStates = (state: PlayState) => state;
// 另外声明getPlayer
export const getPlayer = createFeatureSelector<PlayState>('player');

export const getPlaying = createSelector(selectPlayerStates, (state: PlayState) => state.playing)
export const getPlayList = createSelector(selectPlayerStates, (state: PlayState) => state.playList)
export const getSongList = createSelector(selectPlayerStates, (state: PlayState) => state.songList)
export const getPlayMode = createSelector(selectPlayerStates, (state: PlayState) => state.playMode)
export const getCurrentIndex = createSelector(selectPlayerStates, (state: PlayState) => state.currentIndex)
export const getCurrentSong = createSelector(selectPlayerStates, ({ playList, currentIndex }: PlayState) => playList[currentIndex])
```

页面没有什么变化，但是点击歌单上的播放按钮之后就会播放音乐。
