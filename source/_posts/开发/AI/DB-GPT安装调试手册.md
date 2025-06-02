---
title: bladex-ui常用备忘录
categories:
  - AI
tags:
  - DB-GPT
  - GPT
abbrlink: 1b6d
date: 2025-03-18 14:01:00
---


## docker部署db-gpt

1. 拉取镜像

   ```shell
   docker pull eosphorosai/dbgpt:latest
   ```
2. 拉取text2vec-large-chinese

   ```shell
   # 首先需要安装git-lfs（自己百度），记得存放在挂载卷所在的位置
   git clone https://huggingface.co/GanymedeNil/text2vec-large-chinese
   ```
3. 拉取需要的模型

   ```shell
   # 魔塔模型库：https://www.modelscope.cn/models?page=1
   git克隆一下，记得对比文件大小，记得存放在挂载卷所在的位置
   ```
4. 运行docker

   ```shell
   docker run --ipc host --gpus all -d \
   -p 5670:5670 \
   -e LOCAL_DB_TYPE=mysql \
   -e LOCAL_DB_USER=root \
   -e LOCAL_DB_PASSWORD=123456 \
   -e LOCAL_DB_HOST=192.168.214.134 \   #mysql配置改一下
   -e LOCAL_DB_PORT=3306 \
   -e LOCAL_DB_NAME=dbgpt \
   -e LLM_MODEL=Qwen2.5-1.5B-Instruct \   #模型配置改一下
   -e LLM_MODEL_PATH=/app/models/Qwen2.5-1.5B-Instruct \  #模型在容器内的路径（只需要修改模型名字）
   -e LANGUAGE=zh \
   -v /data/models:/app/models \
   --name dbgpt \
   eosphorosai/dbgpt:latest
   ```

## Ubuntu安装DB-GPT

1. 下载源码

   ```shell
   git clone https://github.com/eosphoros-ai/DB-GPT.git
   ```
2. 安装miniconda

   ```shell
   # 进入root账户
   sudo -i

   # 下载文件
   wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

   # 添加可执行权限
   chmod +x Miniconda3-latest-Linux-x86_64.sh

   # 运行安装脚本
   ./Miniconda3-latest-Linux-x86_64.sh

   # 运行后：
   ## 点击ENTER进入即可。之后会出现一个用户协议，按空格键直到协议最底部，然后输入“yes”接受协议，开始安装。之后需要确认想要安装的目录。
   ## 一般就是安在主目录下就行（如上图那种格式），我就是将它安装在主目录下，如果你想更改路径请输入路径后在按回车键。
   ## 之后输入yes即可，之后就显示安装成功了！

   # 刷新路径信息
   source  ~/.bashrc
   ```
3. 新建conda虚拟环境

   ```shell
   conda create -n dbgpt_env python=3.10
   conda activate dbgpt_env
   ```
4. 安装

   ```shell
   pip install -e ".[default]"

   # 安装途中会有报错显示需要torch
   ## 执行下面的命令
   conda install pytorch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 cpuonly -c pytorch
   # 注意：安装中由于有些文件需要外网环境，所以需要提前配置
   ```
5. 配置文件

   ```shell
   cp .env.template  .env
   # 配置修改见下面。
   # 下载embedding model
   git clone https://www.modelscope.cn/Jerry0/text2vec-large-chinese.git
   ```
6. 运行程序

   ```shell
   # 在项目中执行下面的命令
   python dbgpt/app/dbgpt_server.py
   ```
7. 运行成功后配置成后台服务

   ```shell
   # 使用nano或vim
   nano /etc/systemd/system/dbgpt.service
   ```

   ```shell
   #输入下面的配置

   [Unit]
   Description=dbgpt
   After=network.target

   [Service]
   User=root
   Group=root
   WorkingDirectory=/app/dbgpt/DB-GPT/
   ExecStart=/app/miniconda/miniconda3/envs/dbgpt_env/bin/python /app/dbgpt/DB-GPT/dbgpt/app/dbgpt_server.py
   Restart=always
   Environment=PYTHONUNBUFFERED=1

   [Install]
   WantedBy=multi-user.target
   ```

   ```shell
   # 注意：ExecStart中是要执行的命令或脚本，需要使用conda中的虚拟环境中的python来执行。
   ##查看conda的环境配置
   ##查看环境列表和位置
   conda env list
   确认环境位置后添加上/bin/python


   Description: 服务的描述。
   After: 指定网络启动后再启动此服务。
   User 和 Group: 运行该服务的用户和组。
   WorkingDirectory: 工作目录路径。
   ExecStart: 要执行的命令或脚本。
   Restart: 始终重启服务。
   Environment: 设置环境变量，这里设置了PYTHONUNBUFFERED以便实时看到输出。
   WantedBy: 指定目标运行级别。
   ```

   ```shell
   # 加载配置
   systemctl daemon-reload
   # 启动服务
   systemctl start dbgpt
   # 动态查看日志
   journalctl -u  dbgpt -f
   ```

## 注意事项：

1. 分词模型必须存在运行目录的model文件夹下

   ~~~shell
   # 必须存在
   text2vec-large-chinese
   ~~~

## 配置文件

### 基础配置

~~~shell
# 数据库
LOCAL_DB_TYPE=mysql
LOCAL_DB_USER=root
LOCAL_DB_PASSWORD=Huanfa@2025
LOCAL_DB_HOST=192.168.2.20
LOCAL_DB_PORT=3306
LOCAL_DB_NAME=dbgpt



# 模型
LIMIT_MODEL_CONCURRENCY=5
MAX_POSITION_EMBEDDINGS=4096
QUANTIZE_QLORA=True
QUANTIZE_8bit=True
~~~

env文件

### 代理模型

~~~shell
# 模型名称
MODEL_NAME="梁溪环发AI"
# 模型类型
LLM_MODEL=ollama_proxyllm
# 模型路径
MODEL_PATH=ollama_proxyllm
# 模型地址
MODEL_SERVER=http://192.168.12.210:11434
# 代理地址
PROXY_SERVER_URL=http://192.168.12.210:11434
# 代理模型后端
PROXYLLM_BACKEND=qwen2.5:14b
# 代理模型key
PROXY_API_KEY=not_used
# 嵌入类型
EMBEDDING_MODEL=proxy_ollama

proxy_ollama_proxy_server_url=http://192.168.12.210:11434
proxy_ollama_proxy_backend=nomic-embed-text:latest
~~~

### Qwen2.5-1.5B-Instruct模型

~~~shell
# 模型名称
LLM_MODEL=Qwen2.5-1.5B-Instruct
# 模型位置
LLM_MODEL_PATH=/app/models/Qwen2.5-1.5B-Instruct
~~~

## 在线添加模型

Model name：（随便填）

Model path：ollama_proxyllm（默认）

proxy_server_url：（ollama服务地址）

proxy_api_key：not_used（固定值）

proxyllm_backend：（ollama模型）

model_type：（固定值）

![image-20250312160723213](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250312160723213.png)

## Ollama

1. 安装ollama后，配置**用户变量**

~~~shell
# 配置ip及其端口
OLLAMA_HOST=192.168.12.210:11434
# 配置模型存储位置
OLLAMA_MODELS=D:\ollama\
~~~

2. 安装模型[Ollama Search](https://ollama.com/search)

‍
