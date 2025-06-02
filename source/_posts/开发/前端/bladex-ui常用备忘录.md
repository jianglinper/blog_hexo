---
title: bladex-ui常用备忘录
categories:
  - 前端
tags:
  - vue
  - bladex
abbrlink: 1b6d
date: 2025-02-26 18:02:00
---

## 获取localstorage缓存数据

```JavaScript

import { mapGetters } from 'vuex';


computed: {  
  ...mapGetters(['userInfo', 'permission', 'roles'])  
}
```

## join

~~~js
const array = ["Hello", "World"];
const result = array.join(",");
console.log(result); // 输出: "Hello,World"
~~~
