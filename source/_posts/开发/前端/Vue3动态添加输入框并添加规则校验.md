---
title: Vue3动态添加输入框并添加规则校验
categories:
  - 前端
tags:
  - vue
abbrlink: '4436'
date: 2025-02-26 09:55:00
---

## 前言

利用Vue 3的响应式能力和组合式API，可以轻松实现动态添加输入框及其实时校验。通过`v-model`绑定输入框值，结合自定义规则与`watch`监听数据变化，开发者能够灵活地扩展表单功能并确保输入内容的有效性。本文将简明介绍如何运用Vue 3实现这一过程，让您的应用交互更加智能高效。

## 正文

```vue
<template>
  <div>
      <el-form :model="info" ref="forms">
          <el-table
          ref="tableRef"
          :data="info.data"
          border>
          <el-table-column align="center" property="name" label="*姓名">
              <template #default="row">
                  <el-form-item :prop="'data.' + row.$index + '.name'" :rules="formRules.name">
                      <el-input placeholder="请输入姓名" v-model="info.data[row.$index].name" />
                  </el-form-item>
              </template>
          </el-table-column>
          <el-table-column align="center" property="role" label="角色">
              <template #default="row">
                  <el-form-item :prop="'data.' + row.$index + '.role'" :rules="formRules.role">
                      <el-input placeholder="请输入角色" v-model="info.data[row.$index].role" />
                  </el-form-item>
              </template>
          </el-table-column>
          </el-table>
      </el-form>
    <el-button type="primary" @click="submitForm()">Submit</el-button>
  </div>
</template>
```

```vue
<script setup lang="ts">
import {ref, reactive} from 'vue'
import type { FormInstance } from 'element-plus'
let info:any = reactive({
  data:[
      {
      id: 0,
      name: '',
      role:''
      },{
        id: 1,
        name: '',
        role:''
      }
  ]
})
const formRules = reactive({
  name: [{ required: true, message: '请输入姓名', trigger: 'change' }],
  role: [{ required: true, message: '请输入角色', trigger: 'change' }]
})
const forms = ref<FormInstance>()
const submitForm = async () => {
  if (!forms) return
  return await forms.value?.validate((valid: any) => {
      if (valid) {
      console.log('submit!')
      } else {
      console.log('error submit!')
      return false
      }
  })
}
</script>
```
