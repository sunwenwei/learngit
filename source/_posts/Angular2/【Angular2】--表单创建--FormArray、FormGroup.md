---
title: 【Angular2】--表单创建(FormArray、FormGroup)
tags: Angular2
categories: Angular2
abbrlink: 140ab620
date: 2020-10-29 10:53:40
description:
---

# 简述
**表单的三大基本构造块：FormGroup 、 FormControl、FormArray 。**
> **FormGroup** 把每个子 FormControl 的值聚合进一个对象，它的 key 是每个控件的名字。 它通过归集其子控件的状态值来计算出自己的状态。 比如，如果组中的任何一个控件是无效的，那么整个组就是无效的。

> **FormArray** 聚合了数组中每个表单控件的值。 它还会根据其所有子控件的状态总结出自己的状态。比如，如果 FromArray 中的任何一个控件是无效的，那么整个数组也会变成无效的。

简要概述就是在创建表单时，有些数据格式并不是单单的字符串和数值类型的，可能是对象或者是数组，这时候就能用上表单的FormArray和FormGroup，以达到快速获取表单值并提交对应的数据类型。
<!--more-->

****

# 一、场景
假如我们需要提交个人信息，信息需要提交你和家人的姓名和年龄，以及家庭住址。
![表单页](https://img-blog.csdnimg.cn/20201028160948953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)

想到类似的表单页，就简要的写了一个，其中个人信息的姓名和年龄按照正常的字符串提交，家庭信息以数组方式提交，家庭地址按照对象提交。

****
# 实现
##  form.component.html
```html
<!--ant卡片组件-->
<nz-card>
  <form nz-form [formGroup]="formData">
  <!--个人信息-->
    <nz-divider nzText="个人信息" nzOrientation="left"></nz-divider>
    <div nz-row>
      <div nz-col nzSpan="8" nzOffset="2">
        <nz-form-item>
          <nz-form-label nzSpan="6" nzOffset="1">姓名 </nz-form-label>
          <nz-form-control>
            <input nz-input placeholder="请输入名称" formControlName="name" />
          </nz-form-control>
        </nz-form-item>
      </div>
      <div nz-col nzSpan="8" nzOffset="2">
        <nz-form-item>
          <nz-form-label nzSpan="6" nzOffset="1">年龄 </nz-form-label>
          <nz-form-control>
            <nz-input-number [nzMin]="0" [nzStep]="1" formControlName="age"></nz-input-number>
          </nz-form-control>
        </nz-form-item>
      </div>
    </div>
	<!--家庭信息-->
    <nz-divider nzText="家庭信息" nzOrientation="left"></nz-divider>
    <div nz-row *ngFor="let info of family; let i = index" formArrayName='family'>
      <div nz-col nzSpan="8" nzOffset="2">
        <nz-form-item>
          <nz-form-label nzSpan="6" nzOffset="1">{{info.code}} </nz-form-label>
          <nz-form-control [formGroupName]="i">
            <input nz-input placeholder="请输入姓名" formControlName="name" />
          </nz-form-control>
        </nz-form-item>
      </div>
      <div nz-col nzSpan="8" nzOffset="2">
        <nz-form-item>
          <nz-form-label nzSpan="6" nzOffset="1">年龄 </nz-form-label>
          <nz-form-control [formGroupName]="i">
            <nz-input-number [nzMin]="0" [nzStep]="1" formControlName="age"></nz-input-number>

          </nz-form-control>
        </nz-form-item>
      </div>
    </div>
	<!--家庭地址-->
    <nz-divider nzText="家庭地址" nzOrientation="left"></nz-divider>
    <div nz-row>
      <div nz-col nzSpan="24" formGroupName='address'>
        <nz-form-item>
          <nz-form-label nzSpan="4" nzOffset="0">地址</nz-form-label>
          <nz-form-control nzSpan="4">
            <input nz-input placeholder="请输入国家" formControlName="county" />
          </nz-form-control>
          <nz-form-control nzSpan="4" nzOffset="1">
            <input nz-input placeholder="请输入省份" formControlName="province" />
          </nz-form-control>
          <nz-form-control nzSpan="4" nzOffset="1">
            <input nz-input placeholder="请输入市县" formControlName="city" />
          </nz-form-control>
        </nz-form-item>
      </div>
    </div>
  </form>
</nz-card>
<br />
<!--显示数据-->
{{formData.value | json}}

```


****


## form.component.ts
```typescript
	
  constructor(private fb: FormBuilder) {} // 注入formBuilder，快速创建表单

  formData: FormGroup; // 表单数据
  // 模拟简单的家庭信息模板，真实的家庭信息数据量很大
  family = [
    { id: 1, code: '父亲', name: 'father', age: 0 },
    { id: 2, code: '母亲', name: 'mother', age: 0 },
  ];

  ngOnInit(): void {
    // 初始化表单
    this.formData = this.fb.group({
      name: [''],
      age: [''],
      family: this.fb.array([]),
      address: this.fb.group({}),
    });
    // 获取表单组的address参数作为FormGroup，以便于调用FormGroup的添加formControl的方法
    const address = this.formData.controls.address as FormGroup;
    address.addControl('county', new FormControl(null));
    address.addControl('province', new FormControl(null));
    address.addControl('city', new FormControl(null));
    // 获取表单组的family参数作为formarray，以便于调用FormArray的方法
    const familyArray = this.formData.controls.family as FormArray;
    // 循环创建formcontrol，用新的formgroup添加control，再调用formarray的push方法（在数组末尾添加）
    this.family.forEach(() => {
      const familyGroup = new FormGroup({});
      familyGroup.addControl('name', new FormControl(null));
      familyGroup.addControl('age', new FormControl(null));
      familyArray.push(familyGroup);
    });
    
  }

```

****
# 效果
- 输入数据后：
![](https://img-blog.csdnimg.cn/20201028162827879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bmdvb2RsdWNrNjY2,size_16,color_FFFFFF,t_70#pic_center)
- 表单数据结构：
![](https://img-blog.csdnimg.cn/20201028162917845.png#pic_center)

# 总结
更多的是动态创建表单，这时候在添加form control时要注意，往往需要一个唯一标识。如果formArrayName根据索引，并且对象内的属性命名跟索引有关联，那在删除的时候就要注意，再删了第一条数据之后，后边的索引都前移了一位，以至于找不到对应的formControl。