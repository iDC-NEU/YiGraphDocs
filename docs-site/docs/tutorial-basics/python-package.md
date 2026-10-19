---
sidebar_position: 2
title: Python 包安装与使用
---

# YiGraph 2.0 Python 包安装与使用

YiGraph 的 Python 发行包名为 `yigraph`，公共接口为 `import yigraph`。
原有 `aag.*` 导入路径继续可用。需要 Python 3.11 或更新版本。

基础包提供本地图计算、算法知识库和 NetworkX MCP 服务；
`engine` 扩展提供完整的 AAG 智能体运行依赖，`pyg` 扩展提供图学习依赖。
Python 包不包含 Next.js 前端，Web 2.0 界面仍从源码仓库启动。

## 1. 安装

建议创建独立环境：

```bash
python -m venv .venv
# Linux / macOS
source .venv/bin/activate
# Windows PowerShell
# .\.venv\Scripts\Activate.ps1
```

### 从 PyPI 安装

2.0.0 已发布到 [PyPI](https://pypi.org/project/yigraph/2.0.0/)：

```bash
python -m pip install yigraph==2.0.0
yigraph --version
```

### 从 main 源码安装

```bash
git clone --branch main https://github.com/iDC-NEU/YiGraph.git
cd YiGraph
python -m pip install .
yigraph --version
```

开发时使用 `python -m pip install -e .`。请使用包含 `pyproject.toml` 的版本。

### 构建和安装发行包

在仓库根目录执行：

```bash
python -m pip install build twine
python -m build
python -m twine check dist/*
```

构建生成 `dist/yigraph-2.0.0-py3-none-any.whl` 和 `dist/yigraph-2.0.0.tar.gz`。
可将 wheel 交给其他同学，在另一台机器安装：

```bash
python -m pip install ./dist/yigraph-2.0.0-py3-none-any.whl
```

普通使用者可直接从 PyPI 安装；源码和 wheel 安装适合开发或离线分发。

## 2. 运行无需外部服务的示例

```bash
yigraph demo
# 等价入口：python -m yigraph demo
```

示例直接调用 YiGraph 原有的 `GraphComputationProcessor`，构建一个有向图，
计算最短路径和 PageRank。输出中 `shortest_path_A_to_D` 应为 `["A", "C", "D"]`，
PageRank 得分最高的节点为 `D`。这一步不需要 API Key、GPU 或数据库。

也可以直接在 Python 中使用：

```python
from yigraph import EdgeData, GraphComputationProcessor, VertexData

processor = GraphComputationProcessor()
processor.create_graph_from_edges(
    [VertexData(name) for name in ("A", "B", "C")],
    [EdgeData("A", "B"), EdgeData("B", "C")],
    directed=True,
)
print(processor.run_shortest_path("A", "C"))  # ["A", "B", "C"]
print(processor.run_pagerank())
```

源码仓库中还可运行 `python examples/graph_analysis.py`。

## 3. 初始化可写工作目录

```bash
yigraph init ./yigraph-workspace
```

该命令复制不含真实密钥的配置模板、算法知识库及演示数据：

```text
yigraph-workspace/
  config/
    engine_config.yaml
    data_upload_config.yaml
    config_servers.yaml
  knowledge_base/
  datasets/
    data/
    dataset_schemas/
```

重复运行会保留已经存在的文件。配置和用户数据写入工作目录，避免修改安装环境。
在 Python 代码导入 `aag` 模块前设置 `YIGRAPH_HOME`，例如：

```python
import os
from pathlib import Path

os.environ["YIGRAPH_HOME"] = str(Path("./yigraph-workspace").resolve())
from yigraph import load_config

config = load_config()
print(config.retrieval.embedding["device"])  # cpu
```

也可在终端设置绝对路径：

```bash
# Linux / macOS
export YIGRAPH_HOME="/absolute/path/to/yigraph-workspace"
```

```powershell
# Windows PowerShell
$env:YIGRAPH_HOME = (Resolve-Path ./yigraph-workspace).Path
```

命令行的 `--home` 可代替环境变量。未指定时，源码运行沿用仓库配置；
wheel 安装默认查找用户目录下的 `.yigraph`，可用 `yigraph init ~/.yigraph` 初始化。

## 4. 使用完整智能体

### 安装运行依赖

从 PyPI 安装完整引擎：

```bash
python -m pip install "yigraph[engine]==2.0.0"
# 需要 PyG 图学习算法时：
python -m pip install "yigraph[engine,pyg]==2.0.0"
```

在源码仓库中也可以执行：

```bash
python -m pip install ".[engine]"
# 需要 PyG 图学习算法时：
python -m pip install ".[engine,pyg]"
```

从 wheel 安装时：

```bash
python -m pip install "./dist/yigraph-2.0.0-py3-none-any.whl[engine]"
```

`engine` 会安装模型、嵌入及数据库客户端依赖，下载量较大。
PyTorch 的 CPU/CUDA 版本按机器环境选择；基础安装不强制安装 Linux CUDA 库。

### 配置服务

编辑工作目录中的 `config/engine_config.yaml`：

- `reasoner.llm`：配置模型服务地址、API Key、模型名，或切换到 Ollama。
- `retrieval.embedding`：配置嵌入模型及设备；模板默认使用 `cpu`。
- `retrieval.database`：按所用能力配置 NebulaGraph、Milvus 和 Neo4j。
- `config/config_servers.yaml`：配置计算服务；基础安装仅使用 `networkx`，使用 `pyg` 前安装对应扩展，也可删除不使用的 `pyg` 服务项。

Python 包安装的是客户端和程序代码，不会自动部署数据库或模型服务。
完整智能体目前会初始化向量检索等模块，请提前准备配置中需要的服务，
不能仅凭本地最短路径示例成功就认定智能体服务已就绪。

### 在 Python 中发起分析

完成配置后，设置好 `YIGRAPH_HOME`，再运行：

```python
import asyncio
from yigraph import AAGEngine, load_config


async def main():
    engine = AAGEngine(load_config())
    try:
        print(engine.list_datasets())
        engine.specific_dataset("AMLSim1K", "graph")
        result = await engine.run("使用 PageRank 找出最重要的十个账户", mode="normal")
        print(result)
    finally:
        await engine.shutdown()


asyncio.run(main())
```

`run()` 和 `shutdown()` 都需要 `await`。
`mode` 可选 `normal`、`interact`、`expert`；后两种模式返回待确认的 DAG，
可调用 `await engine.expert_modify_dag(...)` 修改，再用
`await engine.expert_start_analysis()` 执行。

仓库提供 `examples/agent_analysis.py`；指定问题的示例：

```bash
python examples/agent_analysis.py --dataset AMLSim1K --question "Find the ten most important accounts using PageRank."
```

### 启动终端交互

```bash
yigraph --home ./yigraph-workspace chat
```

工作目录中附带 AMLSim1K 等演示数据；数据库导入情况取决于本地环境，
请先通过 `engine.list_datasets()` 或终端的 `datasets` 命令检查数据集。

## 5. Web 2.0 与文档同步

Web 2.0 的 Flask 后端和 Next.js 前端保留在 `web/frontend`，
界面运行方法见源码仓库中的 `web/README.md`。Python wheel 用于程序调用和终端运行。

本页在 YiGraph 源码仓库维护，向独立文档仓库同步时执行：

```bash
python scripts/sync_python_docs.py ../YiGraphDocs
python scripts/sync_python_docs.py ../YiGraphDocs --check
```

该命令同时同步中英文教程。进入 `YiGraphDocs/docs-site` 后执行
`npm ci`、`npm run build`，构建成功后再通过文档站现有流程部署。

## 6. 常见问题

- **命令找不到**：激活安装时的虚拟环境，或使用 `python -m yigraph`。
- **找不到配置或知识库**：先初始化目录，并在导入前设置 `YIGRAPH_HOME`，或在 CLI 使用 `--home`。
- **提示缺少 engine 依赖**：执行 `python -m pip install "yigraph[engine]==2.0.0"`。
- **MCP 子进程找不到包**：计算服务默认使用当前 Python 解释器；自定义 `command` 时要确保该解释器也安装了 YiGraph。
- **连接失败或嵌入模型下载失败**：检查外部服务、模型文件及网络；这些步骤与 wheel 安装验证分别进行。

## 7. 维护者发布到 PyPI

2.0.0 已发布，不能再次上传或覆盖。发布后续版本前，请先更新版本号，再执行以下步骤。

在 [PyPI](https://pypi.org/) 完成注册、邮箱验证和双重验证，并创建 API Token。
首次发布新项目需要 Token 有创建项目的权限。在仓库根目录执行：

```bash
python -m pip install ".[dev]"
python -m build
python scripts/publish_pypi.py --configure-token
python scripts/publish_pypi.py --check
python scripts/publish_pypi.py
```

`--configure-token` 通过本机隐藏输入读取 Token，保存到系统凭据管理器，
不会写入仓库。`--check` 只检查发行包与版本，不上传。
正式发布只上传当前版本的 wheel 和源码发行包，且会拒绝覆盖 PyPI 上的已有版本。
发布成功后，用户可通过 `python -m pip install yigraph==2.0.0` 安装。
后续版本请先更新版本号，再构建和发布。
