---
title: Table添加序号
categories:
  - 前端
tags:
  - vue
abbrlink: 302a
date: 2025-01-01 09:10:00
---


## Vue 添加从1开始 添加序号

**在template 添加**

```vue
 <el-table-column label="序号" align="center" type="index" :index="indexMethod"/>
```

**在script添加**

```js
/** 自定义编号 */
 indexMethod(index) {
    let pageNum = this.queryParams.pageNum - 1;
    if ((pageNum !== -1 && pageNum !== 0)) {
        return (index + 1) + (pageNum * this.queryParams.pageSize);
   } else {
        return (index + 1)
   }
 }
```
