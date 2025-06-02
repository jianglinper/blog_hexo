---
title: 播放FLV在线流
categories:
  - 前端
tags:
  - vue
  - flv
abbrlink: '3e17'
date: 2025-01-01 10:55:00
---


## 安装依赖

```shell
npm install vue flv.js --save
```

## 页面代码

```vue
<template>
  <div>
    <video ref="videoPlayer" controls width="640" height="360"></video>
  </div>
</template>

<script>
import flvjs from 'flv.js';

export default {
  data() {
    return {
      flvPlayer: null,
      videoSource: 'http://58.215.18.181:26088/flv?port=26935&app=gblive&stream=32021368001310543292' // 替换为你的 HTTP-FLV 视频流地址
    };
  },
  mounted() {
    this.initFlvPlayer();
  },
  beforeDestroy() {
    this.destroyFlvPlayer();
  },
  methods: {
    initFlvPlayer() {
      if (flvjs.isSupported()) {
        const videoPlayer = this.$refs.videoPlayer;
        const flvPlayer = flvjs.createPlayer({
          type: 'flv',
          url: this.videoSource
        });

        flvPlayer.attachMediaElement(videoPlayer);
        flvPlayer.load();
        flvPlayer.play();

        this.flvPlayer = flvPlayer;
      } else {
        console.error('FLV.js is not supported in this browser.');
      }
    },
    destroyFlvPlayer() {
      if (this.flvPlayer) {
        this.flvPlayer.destroy();
      }
    }
  }
};
</script>

<style scoped>
/* 可以在这里添加一些样式 */
</style>

```
