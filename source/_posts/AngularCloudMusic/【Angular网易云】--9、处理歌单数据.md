---
title: 【Angular网易云】--9、处理歌单数据
tags: CloudMusic
categories: CloudMusic
abbrlink: baceef39
date: 2020-11-15 17:23:38
---
 
# 场景
 home首页渲染歌单列表，再点击列表的播放图标之后获取单个歌单的信息（应包含歌曲的URL，以便于在首页底部添加播放）

 <!--more-->

****
# 组件传参
歌单的列表是嵌套在父组件中的单独组件，所以再点击子组件时，将获取得到ID广播到父组件，父组件拿到之后再做处理。
- single-sheet.component.html
```html
</div>
<!--歌单点击事件-->
   <i class="icon play" (click)="playSheet(sheet.id)"></i>
 </div>
```
- single-sheet.component.ts
```typescript
export class SingleSheetComponent implements OnInit {

  @Output() onPlay = new EventEmitter<number>();

  playSheet(id: number){
    this.onPlay.emit(id)
  }
}
```
子组件将拿到的ID广播出去

- home.component.html
 ```html
 <!--热门歌单开始-->
          <div class="down">
            <div class="down-wrap">
             <!-- 父组件拿到子组件的传值后，进行回调-->
              <app-single-sheet
                class="sheet-item"
                [sheet]="item"
                (onPlay)="onPlaySheet($event)"
                *ngFor="let item of songSheet"
              ></app-single-sheet>
            </div>
          </div>
          <!--热门歌单结束-->
```
- home.component.ts
>这里有一个注意项，因为单个api不能获取到想要的数据，所以要将两个接口的返回值进行拼接。
```typescript
  // 点击歌单，获取歌单信息
  onPlaySheet(id: number){
    this.sheetServe.playSheet(id).subscribe((playlist) => {
        console.log('playList', playlist);
    })
  }
```
****

# 合并返回值
- sheet.service.ts
```typescript
  // 获取歌单详情
  getSongSheetDetail(id: number): Observable<SongSheet> {
    const params = new HttpParams().set('id', id.toString());
    return this.http.get(this.uri + "playlist/detail", { params })
      .pipe(map((res: { playlist: SongSheet }) => res.playlist));
  }
  // 获取歌单详细信息-拼接URL
  playSheet(id: number): Observable<Song[]>{
    return this.getSongSheetDetail(id)
    .pipe(pluck('tracks'), switchMap(tracks => this.songServe.getSongList(tracks)))
  }
```
playSheet回调中先获取歌单详情信息（getSongSheetDetail），然后将返回值中的playlist属性作为函数返回值，拿到之后在筛选tracks属性的值。**pluck用于把结构中的字段取出合并到一个数组中**


****
- song.service.ts
```typescript
  // 获取歌曲url
  getSongUrl(ids: string): Observable<SongUrl[]> {
    const params = new HttpParams().set('id', ids);
    return this.http.get(this.uri + "song/url", { params })
      .pipe(map((res: { data: SongUrl[] }) => res.data));
  }

  // 拼接歌曲列表和单条歌曲url
  private generateSongList(songs: Song[], urls: SongUrl[]):Song[]{
      const result = [];
      songs.forEach( song => {
          const url = urls.find(url => url.id === song.id).url;
        if(url){
            result.push({...song,url});
        }
        })
        return result;
  }

  // 获取歌曲列表
  getSongList(songs:Song | Song[]): Observable<Song[]>{
      const songArr = Array.isArray(songs) ? songs.slice() : [songs];
      const ids = songArr.map(item =>item.id).join(',');
      return this.getSongUrl(ids).pipe(map(urls => this.generateSongList(songArr, urls)));
      
  }
```
在新增的song服务中添加回调，在获取到歌单列表之后就要获取歌单中歌曲的URL，并且拼接到返回值中。

