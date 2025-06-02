---
title: el上传图片案例
categories:
  - 前端
tags:
  - vue
abbrlink: '9863'
date: 2025-02-26 18:20:00
---



## 效果


## Code

### Vue

```html
<el-form-item label="现场照片" prop="urls">  
  <el-upload  
    v-model:file-list="imgFileList1"  
    :action="action"  
    :headers="imgToken"  
    :on-success="handleSuccess1"  
    list-type="picture-card"  
    :before-upload="beforeAvatarUpload"  
    :on-preview="handlePictureCardPreview"  
    :on-remove="handleRemove1" "  
  >  
    <el-icon>  
      <Plus />  
    </el-icon>  
  </el-upload>  
</el-form-item>
```

### Data

```javascript
//上传图片路径  
imgFileList1: [], //图片回显时使用  
imgFileListForm: [], //图片回显时使用  
piclist1: [], //图片回显时使用  
imgToken: {  
  'Blade-Auth': 'Bearer ' + JSON.parse(localStorage.getItem('saber-token')).content  
},  
limitImg: 10, //限制图片上传的数量  
action: `/api/blade-resource/oss/endpoint/put-file`,  
disabled: false,  
dialogImageUrl: '', //如果只是显示一张图片的话，可以直接对其赋值  
dialogVisible: false
```

### methods

```javascript
beforeAvatarUpload(file) {  
  const imgType = file.type === 'image/jpeg' || file.type === 'image/png';  
  const imgSize = file.size / 1024 / 1024 < 5;  
  if (!imgType) {  
    this.$utils.toast('图片只能是 JPG/PNG 格式!', 'error');  
  }  
  if (!imgSize) {  
    this.$utils.toast('图片大小不能超过 5MB!', 'error');  
  }  
  return imgType && imgSize;  
},  
  
handlePictureCardPreview(uploadFile) {  
  this.dialogImageUrl = uploadFile.url;  
  this.dialogVisible = true;  
},  
  
handleRemove1(res) {  
  console.log(res);  
  let index = this.imgFileListForm.findIndex(e => e === res.url);  
  this.imgFileListForm.splice(index, 1);  
  console.log(this.imgFileListForm);  
},  
  
handleSuccess1(res) {  
  if (res.code === 200) {  
    this.imgFileListForm.push(res.data.link);  
  }  
},
```

### 图片回显

```JavaScript
//在查询到数据的地方
if (this.form.images) {  
  this.form.images.forEach(value => {  
    this.imgFileList1.push({ url: value });  
  });  
}
```
