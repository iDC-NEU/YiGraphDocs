# YiGraph Windows 本地部署指南

本指南详细介绍了如何在 **Windows 系统（CPU 环境）** 本地部署 YiGraph 系统。YiGraph 官方文档主要面向 Linux 环境，本指南针对 Windows 平台特有的问题进行了详细说明，并提供了经过验证的解决方案。

## 目录

- [环境要求](#环境要求)
- [前置依赖安装](#前置依赖安装)
- [下载源码](#下载源码)
- [修改依赖列表](#修改依赖列表)
- [安装 Python 依赖](#安装-python-依赖)
- [配置 Neo4j 图数据库](#配置-neo4j-图数据库)
- [配置 Milvus 向量数据库](#配置-milvus-向量数据库)
- [配置 LLM API](#配置-llm-api)
- [启动服务验证](#启动服务验证)

---

## 环境要求

### 操作系统
- **Windows 10 / Windows 11**（本指南基于 Windows 11 验证）

### 硬件
- **CPU 环境**：本指南针对无 NVIDIA GPU 的环境进行编写
- **内存**：建议至少 16GB 可用内存
- **磁盘空间**：建议至少 20GB 可用磁盘空间

### 软件
- **Python**：3.11 版本（推荐使用 conda 管理环境）
- **Java**：OpenJDK 11（Neo4j 3.5.25 需要）
- **Docker Desktop**：用于运行 Milvus 向量数据库（Windows 环境必需）
- **Git**：用于克隆源码

---

## 前置依赖安装

### 1. 安装 Python 环境（使用 conda）

如果你还没有安装 conda，可以下载安装 [Miniforge3](https://github.com/conda-forge/miniforge)。

```powershell
# 创建虚拟环境
conda create -n AAG python=3.11

# 激活环境
conda activate AAG
```

### 2. 安装 Java 11

> **Windows 特殊问题**：Windows 系统默认不包含 Java，必须手动安装。

Neo4j 3.5.25 需要 Java 11 才能运行。推荐安装 Adoptium OpenJDK 11：

1. 访问 [Adoptium OpenJDK 11 下载页面](https://adoptium.net/temurin/releases/?version=11)
2. 下载 Windows x64 版本的安装程序
3. 运行安装程序，按照提示完成安装
4. 验证安装：

```powershell
java -version
```

应该看到类似输出版本信息：
```
openjdk version "11.0.xx"
OpenJDK Runtime Environment Temurin-11.0.x
OpenJDK 64-Bit Server VM Temurin-11.0.x
```

---

## 下载源码

```powershell
git clone https://github.com/iDC-NEU/YiGraph.git
cd YiGraph
```

---

## 修改依赖列表

> **Windows 特殊问题**：requirements.txt 中包含多个仅 Linux 支持的包，必须注释掉才能安装。

打开 `requirements.txt` 文件，找到以下行并全部注释掉（在行首添加 `#`）：

从 `nvidia-cuda-cupti-cu12` 开始到 `triton` 结束，**一共 14 行**：

```txt
# nvidia-cuda-cupti-cu12==12.1.105
# nvidia-cuda-nvrtc-cu12==12.1.105
# nvidia-cuda-runtime-cu12==12.1.105
# nvidia-cudnn-cu12==8.9.2.26
# nvidia-cublas-cu12==12.1.3.1
# torch-scatter==2.1.1
# torch-sparse==0.6.18
# torch-cluster==1.6.3
# torch-spline-conv==1.2.2
# torch_geometric==2.4.0
# pyg-lib==0.3.0
# fa2-cython==0.3.1
# pynvml==11.5.0
# triton==2.1.0
```

然后，找到 `milvus-lite==2.5.1` 这一行，也注释掉：

```txt
# milvus-lite==2.5.1
```

> **说明**：所有 NVIDIA CUDA 加速包和 milvus-lite 都只支持 Linux 系统，不支持 Windows。对于 CPU 环境，这些包不是必需的。Milvus 需要通过 Docker 在 Windows 上运行。

---

## 安装 Python 依赖

```powershell
pip install -r requirements.txt
```

### 修复 pkg_resources 模块缺失问题

> **Windows 特殊问题**：新版本 setuptools 移除了 `pkg_resources`，会导致启动错误。

如果遇到 `ModuleNotFoundError: No module named 'pkg_resources'` 错误，需要降级 setuptools：

```powershell
pip install setuptools==65.6.0
```

### 安装 CPU 版本 PyTorch

> **Windows 特殊问题**：默认安装的 PyTorch 是 CUDA 版本，如果没有 NVIDIA GPU，需要重新安装 CPU 版本。

```powershell
# 卸载现有的 torch
pip uninstall torch -y

# 安装 CPU 版本
pip install torch --index-url https://download.pytorch.org/whl/cpu
```

---

## 配置 Neo4j 图数据库

### 1. 下载 Neo4j 3.5.25

YiGraph 需要 Neo4j 3.5.25 版本。官方网站旧版本不易找到，可直接使用以下链接下载：

- **Windows 版本**：https://dist.neo4j.org/neo4j-community-3.5.25-windows.zip

下载完成后，解压到你想要的目录，例如：`D:\neo4j\neo4j-community-3.5.25`

### 2. 配置 Neo4j

进入 `conf` 目录，打开 `neo4j.conf` 文件，修改/添加以下配置：

```properties
dbms.connectors.default_listen_address=0.0.0.0
dbms.connectors.default_advertised_address=localhost
dbms.connector.bolt.listen_address=0.0.0.0:7687
dbms.connector.http.listen_address=0.0.0.0:7474
dbms.connector.https.enabled=true
```

### 3. 启动 Neo4j

以管理员身份打开 PowerShell，进入 Neo4j 的 `bin` 目录：

```powershell
cd D:\neo4j\neo4j-community-3.5.25\bin

# 启动 Neo4j（控制台模式，可以看到日志）
.\neo4j.bat console
```

或者，安装为 Windows 服务后台运行：

```powershell
.\neo4j.bat install-service
.\neo4j.bat start-service
```

### 4. 修改默认密码

1. 打开浏览器访问 `http://localhost:7474`
2. 默认用户名：`neo4j`，默认密码：`neo4j`
3. 登录后按照提示修改密码，记住你的新密码
4. 在 YiGraph 配置中使用这个用户名和密码

---

## 配置 Milvus 向量数据库

> **Windows 特殊问题**：milvus-lite 不支持 Windows，必须使用 Docker 运行 Milvus 服务。

### 1. 安装 Docker Desktop

下载并安装 [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)，安装完成后启动 Docker。

### 2. 创建 Milvus 配置文件

创建一个目录用于存放 Milvus 配置，例如 `D:\milvus`，在该目录下创建 `docker-compose.yml` 文件：

```yaml
version: '3.5'

services:
  etcd:
    container_name: milvus-etcd
    image: quay.io/coreos/etcd:v3.5.0
    environment:
      - ETCD_AUTO_COMPACTION_MODE=revision
      - ETCD_AUTO_COMPACTION_RETENTION=1000
      - ETCD_QUOTA_BACKEND_BYTES=4294967296
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/etcd:/etcd
    command: etcd -advertise-client-urls=http://0.0.0.0:2379 -listen-client-urls http://0.0.0.0:2379 --data-dir /etcd
    healthcheck:
      test: ["CMD", "etcdctl", "endpoint", "health"]
      interval: 30s
      timeout: 20s
      retries: 3

  minio:
    container_name: milvus-minio
    image: minio/minio:RELEASE.2023-03-20T20-16-18Z
    environment:
      MINIO_ACCESS_KEY: minioadmin
      MINIO_SECRET_KEY: minioadmin
    ports:
      - "9001:9001"
      - "9000:9000"
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/minio:/minio_data
    command: minio server /minio_data --console-address ":9001"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 30s
      timeout: 20s
      retries: 3

  standalone:
    container_name: milvus-standalone
    image: milvusdb/milvus:v2.5.1
    command: ["milvus", "run", "standalone"]
    security_opt:
    - seccomp:unconfined
    environment:
      ETCD_ENDPOINTS: etcd:2379
      MINIO_ADDRESS: minio:9000
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/milvus:/var/lib/milvus
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9091/healthz"]
      interval: 30s
      start_period: 90s
      timeout: 20s
      retries: 3
    ports:
      - "19530:19530"
      - "9091:9091"
    depends_on:
      - "etcd"
      - "minio"

networks:
  default:
    name: milvus
```

### 3. 启动 Milvus

在 `D:\milvus` 目录下打开 PowerShell，执行：

```powershell
docker-compose up -d
```

首次启动会下载镜像，需要等待几分钟。启动完成后，可以用以下命令查看状态：

```powershell
docker ps
```

应该看到 `milvus-standalone`、`milvus-etcd`、`milvus-minio` 三个容器都在运行。

---

## 配置 LLM API

编辑配置文件 `config/engine_config.yaml`，配置你的 LLM API 信息。

### 火山引擎 Coding Plan 示例配置

如果你使用火山引擎的 Coding Plan 服务，配置如下：

```yaml
reasoner:
  llm:
    provider: "openai"
    openai:
      base_url: "https://ark.cn-beijing.volces.com/api/coding/v3/"
      api_key: "你的API Key"
      model: "ark-code-latest" # 或者其他可用模型名称
```

> **重要提示**：`base_url` **末尾必须加上斜杠 `/`**，否则请求会失败。

### OpenAI 示例配置

```yaml
reasoner:
  llm:
    provider: "openai"
    openai:
      base_url: "https://api.openai.com/v1/"
      api_key: "你的API Key"
      model: "gpt-4o-mini"
```

### 配置向量数据库连接

在同一个配置文件中，确认 Milvus 连接配置：

```yaml
retrieval:
  database:
    vector:
      host: "localhost"
      port: 19530
```

这个配置一般不需要修改，除非你修改了 Milvus 的默认端口。

### 配置 Embedding 设备

修改 embedding 设备为 cpu：

```yaml
retrieval:
  embedding:
    model_name: "BAAI/bge-large-zh-v1.5"
    device: "cpu"  # 这里改为 cpu
```

---

## 启动服务验证

### 1. 确认所有服务正在运行

- [ ] Neo4j 服务已启动（http://localhost:7474 可以访问）
- [ ] Milvus Docker 容器正在运行（端口 19530 已监听）

### 2. 激活 conda 环境

```powershell
conda activate AAG
```

### 3. 启动 YiGraph Web 服务

```powershell
cd YiGraph
python web/frontend/run.py
```

启动成功后，终端会输出类似：

```
* Running on http://0.0.0.0:5000
```

打开浏览器访问 `http://localhost:5000`，应该能看到 YiGraph 的 Web 界面。

### 4. 测试分析功能

1. 选择数据集 AMLSim1K
2. 输入一个测试问题，例如："请帮我分析这个交易网络中最重要的前 10 个账户"
3. 点击发送，观察系统执行流程

如果系统能够完整执行分析，生成 DAG 可视化，并输出分析结果，说明**部署成功**！🎉

---

## 成功部署验证标准

以下是部署成功的标志：

✅ **所有依赖服务正常运行**
- Neo4j 图数据库 - 运行正常
- Milvus 向量数据库 (Docker) - 运行正常
- MCP NetworkX 计算引擎 - 启动成功
- LLM API - 调用成功

✅ **核心分析流程打通**
- 问题路由分类正常
- DAG 构建成功，前端可视化正常
- 算法选择正常
- 依赖分析正常
- 步骤顺序执行正常
- 最终可以输出分析结果

**YiGraph 成功在 Windows CPU 本地环境部署运行** 🎉

---

## 致谢

本部署指南基于实际踩坑经验整理，感谢社区贡献者的测试与反馈。
