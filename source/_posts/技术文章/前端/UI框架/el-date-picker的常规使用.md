---
title: el-date-picker的常规使用
tags:
  - ElementUI
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
  - 前端
  - UI框架
date: 2022-09-21 22:14:44
---


HTML 页面部分

```html
<el-form-item label="时间" prop="testDate">
  <el-date-picker 
    v-model="testDate"
    tyep="daterange"
    start-placeholde="开始日期"
    end-placeholde="结束日期" />
</el-form-item>
```


 JavaScript 部分

```JavaScript
export default {
  data() {
    startTime = '',
    endTime = ''
    ....
  }
  ....
  computed: {
    testDate: {
      get() {
        return [this.startTime, this.endTime]
      },
      set(val) {
        // 此处需要对 val 进行判空，因为 el-date-picker 的清空按钮点击之后会将 testDate 置为 null
        if (val) {
          this.startTime = dateFormate(val[0])
          this.endTime = dateFormate(val[0])
        } else {
          this.startTime = ''
          this.endTime = ''
        }
      }
    }
  }
  method: {
    reset() {
       this.startTime = ''
       this.endTime = ''
     }
  }
}
```



