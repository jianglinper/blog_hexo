---
title: Docker操作命令之 操作镜像
categories:
  - 服务器
tags:
  - Docker
  - 食用教程
abbrlink: c42c
date: 2025-02-25 09:01:33
---

参考：[Docker 命令大全 | 菜鸟教程](https://www.runoob.com/docker/docker-command-manual.html)


## Docker images

`docker images` 命令用于列出本地的 Docker 镜像。

通过 `docker images` 命令，用户可以查看所有已下载或构建的 Docker 镜像的详细信息，如仓库名称、标签、镜像 ID、创建时间和大小。

```bash
docker images [OPTIONS] [REPOSITORY[:TAG]]
```

OPTIONS 说明：

*  **`-a, --all`**: 显示所有镜像（包括中间层镜像）。
*  **`--digests`**: 显示镜像的摘要信息。
*  **`-f, --filter`**: 过滤输出，基于提供的条件。
*  **`--format`**: 使用 Go 模板格式化输出。
*  **`--no-trunc`**: 显示完整的镜像 ID。
*  **`-q, --quiet`**: 只显示镜像 ID

列出所有本地镜像:

```
docker images
```

列出带有摘要信息的镜像:

```
docker images --digests
```

列出所有镜像（包括中间层镜像）:

```
docker images --all
```

使用过滤条件列出镜像:

```
docker images --filter "dangling=true"
```

只显示镜像 ID:

```
docker images --quiet
```

使用自定义格式输出:

```
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.ID}}\t{{.Size}}"
```





## Docker pull

```bash
docker pull  [OPTIONS] NAME[:TAG|@DIGEST]
```

> **NAME**: 镜像名称，通常包含注册表地址（如 *docker.io/library/ubuntu*）。
>
> **TAG**（可选）: 镜像标签，默认为 *latest*。
>
> **DIGEST**（可选）: 镜像的 SHA256 摘要。

**常用选项**

>  ***-a, --all-tags***: 下载指定镜像的所有标签。
>
>  ***--disable-content-trust***: 跳过镜像签名验证。
>
>  ***--platform***: 如果服务器支持多平台，请设置平台。
>
>  ***-q, --quiet***: 抑制详细输出。

### 错误处理和注意事项

在使用 `docker pull`时，可能会遇到一些常见问题：

* 网络问题：如果下载速度慢或无法连接，可以尝试使用加速器或检查网络设置。
* 权限问题：当拉取私有镜像时，需要先登录镜像仓库。

```bash
docker login myregistry.com
```



## Docker tag

`docker tag` 命令用于创建本地镜像的别名（tag），通过为镜像打标签，可以使用更容易记忆的名字或版本号来标识和管理镜像。

```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

> **`SOURCE_IMAGE[:TAG]`** : 源镜像名称和标签，标签默认为 `latest`。
>
> **`TARGET_IMAGE[:TAG]`** : 目标镜像名称和标签，标签默认为 `latest`。



## Docker push

```bash
docker push [OPTIONS] NAME[:TAG]
```

> **`NAME`**: 镜像名称，通常包含注册表地址（如 `docker.io/myrepo/myimage`）。
>
> **`TAG`**（可选）: 镜像标签，默认为 `latest`。

OPTIONS 说明：

*  **--disable-content-trust :** 忽略镜像的校验,默认开启

## Docker save

`docker save` 命令用于将一个或多个 Docker 镜像保存到一个 tar 归档文件中，以便在其他环境中分发或备份。

```bash
docker save [OPTIONS] IMAGE [IMAGE...]
```

> **`IMAGE`**: 要保存的一个或多个镜像名称或 ID。

OPTIONS 说明：

*  **`-o, --output`**: 指定输出文件的路径。



### 示例

1、保存单个镜像到文件

```bash
# 这将 myimage:latest 镜像保存为 myimage.tar 文件。
docker save -o myimage.tar myimage:latest
```

## Docker load

`docker load` 命令用于从由 `docker save` 命令生成的 tar 文件中加载 Docker 镜像。它可以将存档中的镜像和所有层加载到 Docker 中，使其可以在新环境中使用。

```bash
docker load [OPTIONS]
```

OPTIONS 说明：

*  **`-i, --input`**: 指定输入文件的路径。
*  **`-q, --quiet`**: 安静模式，减少输出信息。

### 示例

1、从文件加载镜像

```bash
# 这将从 myimage.tar 文件中加载镜像。
docker load -i myimage.tar
```


