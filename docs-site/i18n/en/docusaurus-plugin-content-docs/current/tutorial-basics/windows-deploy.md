# YiGraph Windows Local Deployment Guide

This guide provides detailed instructions on how to deploy the YiGraph system locally on **Windows (CPU-only environment)**. The official YiGraph documentation primarily targets Linux environments. This guide highlights Windows-specific issues and provides verified solutions.

## Table of Contents

- [System Requirements](#system-requirements)
- [Install Prerequisites](#install-prerequisites)
- [Clone Source Code](#clone-source-code)
- [Modify Dependencies List](#modify-dependencies-list)
- [Install Python Dependencies](#install-python-dependencies)
- [Configure Neo4j Graph Database](#configure-neo4j-graph-database)
- [Configure Milvus Vector Database](#configure-milvus-vector-database)
- [Configure LLM API](#configure-llm-api)
- [Start and Verify](#start-and-verify)

---

## System Requirements

### Operating System
- **Windows 10 / Windows 11** (This guide was tested on Windows 11)

### Hardware
- **CPU-only Environment**: This guide is written for systems without NVIDIA GPU
- **Memory**: Minimum 16GB available RAM recommended
- **Disk Space**: Minimum 20GB available disk space recommended

### Software
- **Python**: Version 3.11 (conda environment recommended)
- **Java**: OpenJDK 11 (required by Neo4j 3.5.25)
- **Docker Desktop**: Required for running Milvus vector database on Windows
- **Git**: For cloning source code

---

## Install Prerequisites

### 1. Install Python Environment (using conda)

If you don't have conda installed, download and install [Miniforge3](https://github.com/conda-forge/miniforge).

```powershell
# Create virtual environment
conda create -n AAG python=3.11

# Activate environment
conda activate AAG
```

### 2. Install Java 11

> **Windows-specific issue**: Java is not included by default on Windows and must be installed manually.

Neo4j 3.5.25 requires Java 11 to run. We recommend installing Adoptium OpenJDK 11:

1. Visit [Adoptium OpenJDK 11 Downloads](https://adoptium.net/temurin/releases/?version=11)
2. Download the Windows x64 installer
3. Run the installer and follow the installation wizard
4. Verify installation:

```powershell
java -version
```

You should see output similar to:
```
openjdk version "11.0.xx"
OpenJDK Runtime Environment Temurin-11.0.x
OpenJDK 64-Bit Server VM Temurin-11.0.x
```

---

## Clone Source Code

```powershell
git clone https://github.com/iDC-NEU/YiGraph.git
cd YiGraph
```

---

## Modify Dependencies List

> **Windows-specific issue**: requirements.txt contains several Linux-only packages that must be commented out for installation to succeed.

Open the `requirements.txt` file, find and **comment out the following lines** by adding `#` at the beginning of each line:

Starting from `nvidia-cuda-cupti-cu12` to `triton`, **total 14 lines**:

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

Then, find the line `milvus-lite==2.5.1` and comment it out as well:

```txt
# milvus-lite==2.5.1
```

> **Note**: All NVIDIA CUDA acceleration packages and milvus-lite only support Linux and do not work on Windows. For CPU-only environments, these packages are not required. Milvus must be run via Docker on Windows.

---

## Install Python Dependencies

```powershell
pip install -r requirements.txt
```

### Fix pkg_resources Module Not Found Error

> **Windows-specific issue**: Newer versions of setuptools have removed `pkg_resources`, which causes startup errors.

If you encounter `ModuleNotFoundError: No module named 'pkg_resources'`, downgrade setuptools:

```powershell
pip install setuptools==65.6.0
```

### Install CPU Version of PyTorch

> **Windows-specific issue**: The default installed PyTorch is CUDA version. If you don't have an NVIDIA GPU, you need to reinstall the CPU version.

```powershell
# Uninstall existing torch
pip uninstall torch -y

# Install CPU version
pip install torch --index-url https://download.pytorch.org/whl/cpu
```

---

## Configure Neo4j Graph Database

### 1. Download Neo4j 3.5.25

YiGraph requires Neo4j version 3.5.25. Older versions are not easily found on the official website. You can download directly using this link:

- **Windows version**: https://dist.neo4j.org/neo4j-community-3.5.25-windows.zip

After downloading, extract to your desired location, for example: `D:\neo4j\neo4j-community-3.5.25`

### 2. Configure Neo4j

Navigate to the `conf` directory, open `neo4j.conf`, and modify/add the following configuration:

```properties
dbms.connectors.default_listen_address=0.0.0.0
dbms.connectors.default_advertised_address=localhost
dbms.connector.bolt.listen_address=0.0.0.0:7687
dbms.connector.http.listen_address=0.0.0.0:7474
dbms.connector.https.enabled=true
```

### 3. Start Neo4j

Open PowerShell as Administrator, navigate to Neo4j's `bin` directory:

```powershell
cd D:\neo4j\neo4j-community-3.5.25\bin

# Start Neo4j (console mode, you can see logs)
.\neo4j.bat console
```

Alternatively, install as a Windows service to run in the background:

```powershell
.\neo4j.bat install-service
.\neo4j.bat start-service
```

### 4. Change Default Password

1. Open your browser and go to `http://localhost:7474`
2. Default username: `neo4j`, default password: `neo4j`
3. After login, follow the prompts to change your password. Remember your new password
4. Use this username and password in your YiGraph configuration

---

## Configure Milvus Vector Database

> **Windows-specific issue**: milvus-lite does not support Windows. Milvus must be run using Docker.

### 1. Install Docker Desktop

Download and install [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/). After installation, start Docker.

### 2. Create Milvus Configuration File

Create a directory for Milvus configuration, for example `D:\milvus`, and create a `docker-compose.yml` file in that directory:

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

### 3. Start Milvus

In PowerShell, navigate to `D:\milvus` and execute:

```powershell
docker-compose up -d
```

First startup will download images, which takes a few minutes. After startup completes, you can check the status with:

```powershell
docker ps
```

You should see three containers running: `milvus-standalone`, `milvus-etcd`, and `milvus-minio`.

---

## Configure LLM API

Edit the configuration file `config/engine_config.yaml` to configure your LLM API information.

### Example Configuration for Volcano Engine Coding Plan

If you use Volcano Engine's Coding Plan service, configure as follows:

```yaml
reasoner:
  llm:
    provider: "openai"
    openai:
      base_url: "https://ark.cn-beijing.volces.com/api/coding/v3/"
      api_key: "Your-API-Key"
      model: "ark-code-latest" # or other available model names
```

> **Important**: `base_url` **must end with a trailing slash `/`**, otherwise the request will fail.

### Example Configuration for OpenAI

```yaml
reasoner:
  llm:
    provider: "openai"
    openai:
      base_url: "https://api.openai.com/v1/"
      api_key: "Your-API-Key"
      model: "gpt-4o-mini"
```

### Configure Vector Database Connection

In the same configuration file, confirm the Milvus connection configuration:

```yaml
retrieval:
  database:
    vector:
      host: "localhost"
      port: 19530
```

This configuration generally doesn't need to be modified unless you've changed Milvus's default port.

### Configure Embedding Device

Change embedding device to cpu:

```yaml
retrieval:
  embedding:
    model_name: "BAAI/bge-large-zh-v1.5"
    device: "cpu"  # Change here to cpu
```

---

## Start and Verify

### 1. Confirm All Services Are Running

- [ ] Neo4j service is started (http://localhost:7474 is accessible)
- [ ] Milvus Docker containers are running (port 19530 is listening)

### 2. Activate conda environment

```powershell
conda activate AAG
```

### 3. Start YiGraph Web Service

```powershell
cd YiGraph
python web/frontend/run.py
```

After successful startup, the terminal will output something like:

```
* Running on http://0.0.0.0:5000
```

Open your browser and go to `http://localhost:5000`. You should see the YiGraph Web interface.

### 4. Test Analysis Functionality

1. Select dataset AMLSim1K
2. Enter a test question, for example: "Please help me analyze the top 10 most important accounts in this transaction network"
3. Click send and observe the system execution flow

If the system can complete the analysis, generate DAG visualization, and output analysis results, then **deployment is successful**!🎉

---

## Successful Deployment Validation Criteria

These are the indicators of a successful deployment:

✅ **All dependency services running normally**
- Neo4j graph database - Running normally
- Milvus vector database (Docker) - Running normally
- MCP NetworkX computation engine - Started successfully
- LLM API - Call successful

✅ **Core analysis pipeline connected**
- Question routing and classification works
- DAG built successfully, frontend visualization normal
- Algorithm selection normal
- Dependency analysis normal
- Step execution in order normal
- Can finally output analysis results

**YiGraph successfully deployed and running on Windows CPU-only local environment** 🎉

---

## Acknowledgments

This deployment guide is compiled based on actual troubleshooting experience. Thank you to community contributors for testing and feedback.
