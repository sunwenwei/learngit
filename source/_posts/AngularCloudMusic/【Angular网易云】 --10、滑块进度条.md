---
title: 【Angular网易云】 --10、滑块进度条
tags: CloudMusic
categories: CloudMusic
abbrlink: 84083a9e
date: 2020-11-20 17:23:38
---
# 场景
首页主体样式大致实现，底部样式还有所欠缺，所以底部应加上个别信息与播放的进度条，这个进度条类似Zorro的滑块，点击与滑动，其中包括事件的绑定与组件的嵌套。其中进度条不仅仅实现在歌曲进度上，在音量控制上也可以复用，为了方便后期在底部添加各种功能而避免代码臃肿，将进度条分离开是最好的选择。

<!--more-->
****


在app.component.html底部中添加新创建的wy-player模块路由 **< app-wy-player>< /app-wy-player>**

- wy-player.component.html -- 临时数据填充
```html
<div class="m-player">
    <div class="lock">
        <div class="left"><i></i></div>
    </div>
    <div class="hand"></div>
    <div class="container">
    <div class="wrap">
        <div class="btns">
          <li class="prev"></li>
          <li class="toggle"></li>
          <li class="next"></li>  
        </div>
        <div class="head">
        <!--临时图片-->
            <img src="https://p1.music.126.net/v_-wonc6yEl9UVa-aPNOSQ==/109951165350855516.jpg" alt="">
            <i class="mask"></i>
        </div>
        <div class="play">
            <div class="words clearfix hide " >
            <!--临时数据-->
                <p class="ellipsis margin-bottom-none">歌名</p>
                <ul  class="songs clearfix margin-bottom-none ">
                    <li>
                        <a>歌手1</a>
                    </li>
                    <li>
                        <a>歌手2</a>
                    </li>
                </ul>
            </div>
            <div class="bar">
                <div class="slider-wrap">
                <!--进度条组件 buffoffset为缓存偏移量，slidervalue为歌曲的偏移量-->
                   <app-wy-slider [bufferOffset]="buffOffset" [(ngModel)]="sliderValue"></app-wy-slider>
                </div>
                <span class="time"> 
                    <em>02:11</em> / 04:35
                </span>
            </div>
        </div>
        <div class="oper">
            <i class="like" title="收藏"></i>
            <i class="share" title="分享"></i>
        </div>
        <div class="ctrl">
            <i class="volume" title="音量"></i>
            <i class="loop" title="循环"></i>
            <p class="open">
                <span></span>
            </p>
            <div class="control-vol">
            <!--进度条组件 vertical表示滑块是否是垂直-->
                <app-wy-slider [wyVertical]="true"></app-wy-slider> 
            </div>
        </div>
    </div>
    </div>
</div>

```
其效果呈现后，获取鼠标点击在进度条的位置传给进度条的组件，从而改变进度条的样式以及位置。所以要在ts中事先声明这两个变量。
****
# wy-slider
- ## wy-slider.component.html
```html
<div class="wy-slider" #wySlider [class.wy-slider-vertical]="wyVertical">
    <app-wy-slider-track [wyVertical]="wyVertical" [wyLength]="bufferOffset" [wyBuffer]="true"></app-wy-slider-track>
    <app-wy-slider-track [wyVertical]="wyVertical" [wyLength]="offset"></app-wy-slider-track>
    <app-wy-slider-handle [wyVertical]="wyVertical" [wyOffset]="offset"></app-wy-slider-handle>
</div>
```
滑块的DOM仅有三个，分别是显示缓存进度，歌曲进度和滑块按钮。

- ## wy-slider.handle.component.ts
```typescript
import { ChangeDetectionStrategy, Component, Input, OnChanges, impleChanges } from '@angular/core';
// 数据类型自定义类
import { WySliderStyle } from './wy-slider-types'; 
@Component({
    selector: 'app-wy-slider-handle',
    template: `<div class="wy-slider-handle" [ngStyle]="style"></div>`,
    changeDetection: ChangeDetectionStrategy.OnPush
})
/*
[ngstyle]
一个属性指令，用于更新容器元素的样式。
可以通过指定用冒号分隔的键值对来设置一个或多个样式属性。
*/
export class WySliderHandleComponent implements OnChanges {

    @Input() wyVertical = false; // 是否是垂直模式
    @Input() wyOffset: number; // 偏移量

    style: WySliderStyle = {} // DOM样式
   
    ngOnChanges (changes: SimpleChanges): void {
        if (changes['wyOffset']) {
            // 偏移量发生改变后，判断是否是垂直，是则更改DOM的bottom样式，否则更改DOM的left样式--更改量都是百分比。
            this.style[this.wyVertical ? 'bottom' : 'left'] = this.wyOffset + '%';
        }
    }

}

```
handle.component.ts中仅需要判断两个值即可，是否是垂直模式和偏移量的值，而偏移量是随时都会变化的，所以引用OnChanges生命周期钩子来实时做出变化。
> [OnChanges]：一个生命周期钩子，当指令的任何一个可绑定属性发生变化时调用。 定义一个 ngOnChanges() 方法来处理这些变更。

- ## wy-slider-track.component.ts

```typescript
import { ChangeDetectionStrategy, Component, Input, OnChanges,SimpleChanges } from '@angular/core';
import { WySliderStyle } from './wy-slider-types';

@Component({
    selector: 'app-wy-slider-track',
    template: `<div class="wy-slider-track" [class.buffer]="wyBuffer" [ngStyle]="style"></div>`,
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class WySliderTrackComponent implements OnInit, OnChanges {

    @Input() wyVertical = false;  // 是否垂直
    @Input() wyLength: number; // 进度条长度
    @Input() wyBuffer: false; // 是否是缓冲，用来判断缓冲条样式
    style: WySliderStyle = {}; // DOM样式
    
    ngOnChanges (changes: SimpleChanges): void {
        // 判断进度条长度是否改变
        if (changes['wyLength']) {
            // 如果是进度条垂直，更改DOM样式的高 -- 更改数值为百分比
            if (this.wyVertical) {
                this.style.height = this.wyLength + '%';
                this.style.left = null;
                this.style.width = null;
            } else {
                // 如果进度条水平，更改DOM样式的宽    --更该数值为百分比
                this.style.width = this.wyLength + '%';
                this.style.bottom = null;
                this.style.height = null;
            }
        }
    }
}

```
与handle.ts的逻辑基本类似，都是根据输入属性的更改来做出样式的更改，track的区别就在于要先判断是否是垂直模式。

- ## wy-slider-types.ts

```typescript
import { Observable } from 'rxjs'
// 滑块样式
export interface WySliderStyle {
    width?: string | null;
    height?: string | null;
    left?: string | null;
    bottom?: string | null;
}

export interface SliderEventObserverConfig {
    start: string;
    move: string;
    end: string;
    filter: (e: Event) => boolean;
    pluckKey: string[];
    startPlucked$?: Observable<number>;
    moveResolved$?: Observable<number>;
    end$?: Observable<Event>;
}

export type SliderValue = number | null;
```

- ## wy-slider.component.ts

```typescript
@Component({
    selector: 'app-wy-slider',
    templateUrl: './wy-slider.component.html',
    styleUrls: ['./wy-slider.component.less'],
    encapsulation: ViewEncapsulation.None, // 调整试图封装模式的默认值Emulated为none--将组件样式升为全局，使子组件也能使用
    changeDetection: ChangeDetectionStrategy.OnPush,
    providers: [{
        provide: NG_VALUE_ACCESSOR,
        useExisting: forwardRef(() => WySliderComponent),
        multi: true
    }]
})
export class WySliderComponent implements OnInit, OnDestroy, ControlValueAccessor {
    @Input() wyVertical = false; // 是否垂直模式
    @Input() wyMin = 0; // 滑块最小值
    @Input() wyMax = 100; // 滑块最大值
    @Input() bufferOffset: SliderValue = 0; // 缓存值

    private sliderDom: HTMLDivElement;  // 用于保存获取到的DOM节点
    // 用于获取#wyslider的节点DOM
    @ViewChild('wySlider', { static: true }) private wySlider: ElementRef 

    private dragStart$: Observable<number>;
    private dragMove$: Observable<number>;
    private dragEnd$: Observable<Event>;
    private dragStart_: Subscription | null;
    private dragMove_: Subscription | null;
    private dragEnd_: Subscription | null;

    private isDragging = false;

    value: SliderValue = null;
    offset: SliderValue = null;

    constructor(
        @Inject(DOCUMENT) private doc: Document, // 原生DOM可能会影响服务端渲染效果，所以使用注入类型
        private cdr: ChangeDetectorRef
    ) { }

    ngOnInit () {
    	// 保存节点
        this.sliderDom = this.wySlider.nativeElement;
        this.createDraggingObservables();
        this.subscribeDrag(['start']);
    }
	 // 添加点击触发事件
    private createDraggingObservables () {
        const origenField = this.wyVertical ? 'pageY' : 'pageX'; // 判断滑块是垂直还是水平
        // 适应PC和移动端，定义mouse和touch类
        const mouse: SliderEventObserverConfig = {
            start: 'mousedown',
            move: 'mousemove',
            end: 'mouseup',
            filter: (e: MouseEvent) => e instanceof MouseEvent,
            pluckKey: [origenField]
        };
        const touch: SliderEventObserverConfig = {
            start: 'touchstart',
            move: 'touchmove',
            end: 'touchend',
            filter: (e: TouchEvent) => e instanceof TouchEvent,
            pluckKey: ['touches', '0', origenField]
        };
        
        [mouse, touch].forEach(source => {
            const { start, move, end, filter: filerFunc, pluckKey } = source;
            
            source.startPlucked$ = fromEvent(this.sliderDom, start)
                .pipe(
                    filter(filerFunc),
                    tap(sliderEvent),
                    pluck(...pluckKey),
                    map((position: number) => this.findClosestValue(position))
                );
            source.end$ = fromEvent(this.doc, end);
            source.moveResolved$ = fromEvent(this.doc, move)
                .pipe(
                    filter(filerFunc),
                    tap(sliderEvent),
                    pluck(...pluckKey),
                    distinctUntilChanged(),
                    map((position: number) => this.findClosestValue(position)),
                    takeUntil(source.end$)
                )
        });
        this.dragStart$ = merge(mouse.startPlucked$, touch.startPlucked$);
        this.dragMove$ = merge(mouse.moveResolved$, touch.moveResolved$);
        this.dragEnd$ = merge(mouse.end$, touch.end$);
    }


    private subscribeDrag (events: string[] = ['start', 'move', 'end']) {
        if (inArray(events, 'start') && this.dragStart$ && !this.dragStart_) {
            this.dragStart_ = this.dragStart$.subscribe(this.onDragStart.bind(this));
        }
        if (inArray(events, 'move') && this.dragMove$ && !this.dragMove_) {
            this.dragMove_ = this.dragMove$.subscribe(this.onDragMove.bind(this));
        }
        if (inArray(events, 'end') && this.dragEnd$ && !this.dragEnd_) {
            this.dragEnd_ = this.dragEnd$.subscribe(this.onDragEnd.bind(this));
        }
    }

    private unsubscribeDrag (events: string[] = ['start', 'move', 'end']) {
        if (inArray(events, 'start') && this.dragStart_) {
            this.dragStart_.unsubscribe();
            this.dragStart_ = null;
        }
        if (inArray(events, 'move') && this.dragMove_) {
            this.dragMove_.unsubscribe();
            this.dragMove_ = null;
        }
        if (inArray(events, 'end') && this.dragEnd_) {
            this.dragEnd_.unsubscribe();
            this.dragEnd_ = null;
        }
    }


    private onDragStart (value: number) {
        console.log('value', value);
        this.toggleDragMoving(true);
        this.setValue(value);
    }
    private onDragMove (value: number) {
        if (this.isDragging) {
            this.setValue(value);
            this.cdr.markForCheck();
        }
    }
    private onDragEnd () {
        this.toggleDragMoving(false);
        this.cdr.markForCheck();
    }

    private setValue (value: SliderValue, needCheck = false) {
        if (needCheck) {
            if (this.isDragging) { return };
            this.value = this.formatValue(value);
            this.updateTrackAndHandles();
        } else if (!this.valuesEqual(this.value, value)) {
            this.value = value;
            this.updateTrackAndHandles();
            this.onValueChange(this.value);
        }
    }

    private formatValue (value: SliderValue): SliderValue {
        let res = value;
        if (this.assertValueValid(value)) {
            res = this.wyMin;
        } else {
            res = limitNumberInrange(value, this.wyMin, this.wyMax)
        }
        return res;
    }

    // 判断是否是NAN
    private assertValueValid (value: SliderValue): boolean {
        return isNaN(typeof value !== 'number' ? parseFloat(value) : value)
    }

    private valuesEqual (valA: SliderValue, valB: SliderValue): boolean {
        if (typeof valA !== typeof valB) {
            return false;
        }
        return valA === valB;
    }

    private updateTrackAndHandles () {
        this.offset = this.getValueToOffset(this.value);
        this.cdr.markForCheck();
    }

    private getValueToOffset (value: SliderValue): SliderValue {
        return getPercent(this.wyMin, this.wyMax, value);
    }

    private toggleDragMoving (movable: boolean) {
        this.isDragging = movable;
        if (movable) {
            this.subscribeDrag(['move', 'end']);
        } else {
            this.unsubscribeDrag(['move', 'end']);
        }
    }


    private findClosestValue (position: number): number {
        // 获取滑块总长
        const sliderLength = this.getSliderLength();
        // 获取滑块左（上）端点位置
        const sliderStart = this.getSliderStartPosition();

        // 滑块当前位置 /  滑块总长
        const ratio = limitNumberInrange((position - sliderStart) / sliderLength, 0, 1);
        const ratioTrue = this.wyVertical ? 1 - ratio : ratio;

        return ratioTrue * (this.wyMax - this.wyMin) + this.wyMin;
    }

    private getSliderLength (): number {
        return this.wyVertical ? this.sliderDom.clientHeight : this.sliderDom.clientWidth;
    }

    private getSliderStartPosition (): number {
        // getElementOffset 在zorro中有api，不确定能否引用
        const offset = getElementOffset(this.sliderDom);
        return this.wyVertical ? offset.top : offset.left;
    }



    private onValueChange (value: SliderValue): void { };
    private onTouched (): void { };

    writeValue (value: SliderValue): void {
        this.setValue(value, true);
    }
    registerOnChange (fn: (value: SliderValue) => void): void {
        this.onValueChange = fn;
    }
    registerOnTouched (fn: () => void): void {
        this.onTouched = fn;
    }



    ngOnDestroy (): void {
        this.unsubscribeDrag();
    }
}

```
其中encapsulation表示样式封装策略，改变其默认值emulated为none，可以使子组件使用上一级的样式，原因是不使用封装后会将样式上升为全局。
![encapsulation](https://img-blog.csdnimg.cn/20201130191257811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70)
在获取节点时使用了viewChild
![viewChild](https://img-blog.csdnimg.cn/20201130192503900.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70)
**在触发时使用fromEvent来为dom容器添加事件转为Observable对象，再用pipe操作符叠加事件处理。先定义filter筛选出mouseEvent与TouchEvent类型，在使用tap监视可观察值做出停止事件传播与清除默认动作，在使用pluck将获取按下的位置，最后使用map将每个observable数据应用findClosestValue函数将位置算出相应的数值**


>- stopPropagation（）
>该方法将停止事件的传播，阻止它被分派到其他 Document 节点。在事件传播的任何阶段都可以调用它。注意，虽然该方法不能阻止同一个 Document 节点上的其他事件句柄被调用，但是它可以阻止把事件分派到其他节点。

> - preventDefault()
> 该方法将通知 Web 浏览器不要执行与事件关联的默认动作（如果存在这样的动作）。例如，如果 type 属性是 "submit"，在事件传播的任意阶段可以调用任意的事件句柄，通过调用该方法，可以阻止提交表单。注意，如果 Event 对象的 cancelable 属性是 fasle，那么就没有默认动作，或者不能阻止默认动作。无论哪种情况，调用该方法都没有作用。

> - pluck()
> 1、把上游数据中特定字段的值"拔"出来，
2、将每个源值(对象)映射成它指定的嵌套属性，
3、选择属性来发出值。
****
避免订阅数量过多，使用merge操作符合并同对的事件。

自定义工具类 array.ts
```typescript
// 判断属性是否在arr中存在
export function inArray(arr: any[], target: any): boolean{
    return arr.indexOf(target) !== -1;
}
```
触发组件的步骤大致为先获取DOM，然后触发createDraggingObservable创建可观察对象，然后根据事件选择相应的订阅事件。而在更新值的时候，变更监测改为onpush，所以在setvalue后需要手动告知Angular才能检查并更新视图(markForCheck)。

****
# 总结
这一节使用了父子组件传值的规则以及声明类的使用，感觉最为复杂的是嵌套的逻辑，对angular的不够熟练迫使我看了视频无数遍，寻找每一个知识点，但还是有些没明白，希望随着进度能够看明白吧。

