---
title: rclone的应用
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - lucky
abbrlink: 81d2
date: 2025-05-24 21:33:08
---
# rclone

```yaml
rclone mount alist-webdav: /vol1/1000/netdisk/alist --header "Referer:" --multi-thread-streams 6 --buffer-size 512M --vfs-fast-fingerprint --vfs-cache-mode full --no-modtime --file-perms 0777 --copy-links --allow-other --allow-non-empty  --umask 000 --daemon --cache-dir /vol1/1000/netdisk/cache/rclone


卸载命令：fusermount -qzu /vol1/1000/netdisk/alist


rclone mount hao: z: --network-mode --header "Referer:" --multi-thread-streams 4 --buffer-size 512M --vfs-fast-fingerprint --vfs-cache-mode full --no-modtime --file-perms 0777
```

‍
