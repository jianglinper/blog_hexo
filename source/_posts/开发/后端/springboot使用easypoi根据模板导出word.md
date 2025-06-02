---
title: SpringBoot 使用 easypoi 根据模板导出word
categories:
  - 后端
tags:
  - SpringBoot
  - easypoi
  - Centos
abbrlink: f6eb
date: 2024-12-18 18:02:00
---


## 前言

项目上有个需求，在 Word 里面导出图片，需要根据固定模板导出文件，故整理

## 导入依赖

在相应的 pom 文件中添加下面的代码

```xml
<dependency>
    <groupId>cn.afterturn</groupId>
    <artifactId>easypoi-base</artifactId>
    <version>4.4.0</version>
</dependency>
```

## 在 resource 中添加文件夹并添加 word 模板

```TEXT
> 在模板中使用{{}}来进行占位
> 需要添加多个图片时，使用{{fe:images t}}来进行占位。 **（其中的 images 替换成自己的字段）** 
> 如图：
```
## 在程序中加入 ExportWordUtil 工具类

详细代码如下：

```java
public class ExportWordUtil {

    /****
     * 导出word数据
     * @param templatePath
     * @param fileName
     * @param params
     */
    public static void exportWord(String templatePath, String fileName, Map<String, Object> params, HttpServletResponse response) {
        Assert.notNull(templatePath, "模板路径不能为空！");
        Assert.notNull(fileName, "文件名称不能为空！");
        Assert.isTrue(fileName.endsWith(".docx"), "word导出请使用docx格式！");
        //生成本地文件时使用，可以添加为参数传进来
//        String tempPath = fileName;
        try {
            XWPFDocument doc = WordExportUtil.exportWord07(templatePath, params);
            // 设置强制下载不打开
            response.setContentType("application/vnd.openxmlformats-officedocument.wordprocessingml.document");
            // 设置文件名
            response.setCharacterEncoding("UTF-8");
            response.addHeader("Content-Disposition", "attachment;fileName=" + URLEncoder.encode(fileName,"UTF-8"));
            OutputStream out = response.getOutputStream();
            doc.write(out);
            out.close();

            //生成本地文件时使用
//            FileOutputStream fos = new FileOutputStream(tempPath);
//            doc.write(fos);
//            fos.close();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //删除本地文件
//            delFileWord(tempPath,fileName);
        }
    }


    @NotNull
    public static List<ImageEntity> getImageEntities(List<String> accident) {
        List<ImageEntity> entities = new ArrayList<>();
        if (accident != null) {
            for (String image : accident) {
                ImageEntity entity = new ImageEntity();
                entity.setHeight(500);
                entity.setWidth(500);
                entity.setUrl(image);
                entity.setType(ImageEntity.URL);
                entities.add(entity);
            }
        } else {
            ImageEntity entity = new ImageEntity();
            entity.setHeight(500);
            entity.setWidth(500);
            entity.setUrl("");
            entity.setType(ImageEntity.URL);
            entities.add(entity);
        }
        return entities;
    }

    /***
     * 删除临时文件
     * @param filePath
     * @param fileName
     */
    private static void delFileWord(String filePath, String fileName){
        File file =new File(filePath+fileName);
        File file1 =new File(filePath);
        file.delete();
        file1.delete();
    }

}
```

其中的 exportWord 方法在本地存储文件时，可以添加一个参数 tempPath 并由程序其他地方传进来；getImageEntities 方法的使用的是 URL 的形式加载图片的，在网上还有一种方法是用 FDSUtils.downloadFile() 下载好图片在传进 word 中。代码如下：

```java
private List<ImageEntity> initImageData(String fileUrl) {
    List<ImageEntity> result = new ArrayList<>();
    if (StringUtils.isNotBlank(fileUrl)) {
        if (fileUrl.indexOf(",") > 0) {
            String[] fileUrlArr = fileUrl.split(",");
            for (int i = 0 ; i < fileUrlArr.length ; i++) {
                byte[] imageData = FDSUtils.downloadFile(fileUrlArr[i]);
                ImageEntity item = new ImageEntity();
                item.setWidth(300);
                item.setHeight(300);
                item.setData(imageData);
                item.setType(ImageEntity.Data);
                result.add(item);
            }
        } else {
            byte[] imageData = FDSUtils.downloadFile(fileUrl);
            ImageEntity item = new ImageEntity();
            item.setWidth(300);
            item.setHeight(300);
            item.setData(imageData);
            item.setType(ImageEntity.Data);
            result.add(item);
        }
    } else {
        ImageEntity item = new ImageEntity();
        item.setWidth(300);
        item.setHeight(300);
        item.setData(null);
        item.setType(ImageEntity.Data);
        result.add(item);
    }
    return result;
}
```

**这种方法我没有试过，其中的 FDSUtils.downloadFile() 方法需要自己封装，这个可以在网上找找！！！**

