---
title: 【Angular2】--创建动态表单
tags:
  - Angular2
categories: Angular2
abbrlink: b1f108b2
date: 2020-10-12 16:20:01
---



# 动态增加表单
这个问题的环境是基于**Angular10**和NG-ZROOR，nz中的动态表单案例是有个触发机制的——点击按钮，而有时候页面的显示是不需要手动触发的。先从后台取回数据，在进行数据的渲染。取回的数据大多是数组，所以案例中的就不可取了，在借鉴了[wujiayucn](https://blog.csdn.net/wjyyhhxit/article/details/91973582) 的《响应式表单-FormArray & 动态的增加、减少FormArray中的元素 & 给FormArray 赋值》一文，我也终于对这个问题有了一丝理解，先做个笔记

<!--more-->
# 场景
**在进入某一个页面的时候，通过获取到的数组，动态渲染到表单中，再提交表单的时候同样以数组形式提交出去**

<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

# 实现：
## page.component.html
```html
<nz-card>
  <form nz-form [formGroup]="validateForm" (ngSubmit)="submitForm()">
    <div *ngFor="let control of tempList; let i = index">
      <nz-form-control [formGroupName]="control.id">
        <div nz-row>
          <div nz-col nzSpan="8">
            <nz-form-label nzSpan="8" nzRequired>name</nz-form-label>
            <nz-form-control nzSpan="16" [nzErrorTip]="nameError">
              <input nz-input formControlName="name" />
              <ng-template #nameError let-name>
                <ng-container *ngIf="name.hasError('required')">
                  name必填
                </ng-container>
              </ng-template>
            </nz-form-control>
          </div>

          <div nz-col nzSpan="8">
            <nz-form-label nzSpan="8" nzRequired>age</nz-form-label>
            <nz-form-control nzSpan="16" [nzErrorTip]="ageError">
              <input nz-input formControlName="age" />
              <ng-template #ageError let-age>
                <ng-container *ngIf="age.hasError('required')">
                  age必填
                </ng-container>
              </ng-template>
            </nz-form-control>
          </div>

          <div nz-col nzSpan="8">
            <nz-form-label nzSpan="8" nzRequired>phone</nz-form-label>
            <nz-form-control nzSpan="16" [nzErrorTip]="phoneError">
              <input nz-input formControlName="phoneNumber" />
              <ng-template #phoneError let-phone>
                <ng-container *ngIf="phone.hasError('required')"></ng-container>
              </ng-template>
            </nz-form-control>
          </div>
        </div>
      </nz-form-control>
    </div>
    <br />
    <nz-form-item>
      <nz-form-control [nzSm]="{ span: 20, offset: 4 }">
        <button nz-button nzType="primary">Submit</button>
      </nz-form-control>
    </nz-form-item>
  </form>
</nz-card>
```
![布局](https://img-blog.csdnimg.cn/20200921132039535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)


<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

## page.component.ts
```javascript
 constructor(private fb: FormBuilder) {} 
  validateForm: FormGroup;
  // 模拟数据：获取到的数组
  tempList = [
    { id: 1, name: '张三', age: 20, phoneNumber: 15855855858 },
    { id: 2, name: '李四', age: 21, phoneNumber: 15855855858 },
    { id: 3, name: '王二', age: 22, phoneNumber: 15855855858 },
  ];
  ngOnInit(): void {
    this.validateForm = this.fb.group({});
    this.addField();
  }

  addField(): void {
  //  根据获取到的数组循环创建controller
    this.tempList.forEach((item: any) => {
      this.validateForm.addControl(
        item.id,
        new FormGroup(
          {
            name: new FormControl([item.name], [Validators.required]),
            age: new FormControl(item.age, Validators.required),
            phoneNumber: new FormControl(item.phoneNumber, [Validators.required]),
          },
          [Validators.required],
        ),
      );
    });
  }
	// 提交表单
  submitForm(): void {
  // 表单脏校验
    for (const i in this.validateForm.controls) {
      this.validateForm.controls[i].markAsDirty();
      this.validateForm.controls[i].updateValueAndValidity();
    }
    const tempValue = []; 
    for (let index = 1; index <= this.tempList.length; index++) {
      const element = this.validateForm.value[index];
      tempValue.push(element);
    }
    console.log('tempValue', tempValue);
  }
```
## tempValue:
![submit](https://img-blog.csdnimg.cn/20200921132757308.png#pic_center)
## validateForm.value:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200921133540611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)


# 残留问题：
在创建controller的时候可以添加同步验证，但是同步验证的结果却是有问题的,虽然能显示要输出的提示，但是存在 ’提示不出现‘ 以及 ’颜色有问题‘，也就是说，创建controller的参数还是不合理的，希望看到的大佬们能够提点一下我，深表感谢！！
>new FormGroup(
          {
            name: new FormControl([item.name], [Validators.required]),
            age: new FormControl(item.age, Validators.required),
            phoneNumber: new FormControl(item.phoneNumber, [Validators.required]),
          },
          [Validators.required],
        ),
![error](https://img-blog.csdnimg.cn/2020092113295940.png#pic_center)

<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

# 总结
动态增加表单时，或许可以以表单分组来循环创建，创建之后的结构会更加分明，之后再解构就能达到想要的结构。该写法或许还有不合理或者未发现的安全隐患，请看到的大佬们给一些反馈和指导，在此感激不尽！
****
# 后续
关于提示问题，已经解决，在每个 **< nz-form-control > </ nz-form-control >** 外层添加一层 **< nz-form-item>** 即可。
![实现](https://img-blog.csdnimg.cn/20200921173132159.png#pic_center)

>表单项用于区分表单中不同的区域，包含表单域和表单标签(可选)。
