---
sidebar_position: 0
---

# 用户上手案例

## 1. 简介

本文档帮助新用户快速了解 **易图** 在图数据集上的典型用法，并借助 **普通 / 交互 / 专家** 三种模式，完成从基础分析到复杂决策的各类任务。

### 三种模式说明

- **普通模式**：用户只需提交业务问题，易图会自动完成问题解析、算法选择与计算执行，并生成分析报告，适合非技术背景或一般业务用户。
- **交互模式**：用户与易图协同分析业务问题。针对给定问题，易图会与大模型交互确定计算流程与图算法，再按既定方案执行并反馈分析报告，适合对业务与图算法有一定了解的进阶用户。
- **专家模式**：用户直接给出业务问题及解决思路、计算步骤与图算法，易图按用户方案执行计算并输出分析报告，适合深度掌握业务与图算法的专家用户。

### 使用方式

- **Web 界面**：聊天界面默认为普通模式，可在界面中选择「交互模式」或「专家模式」。
- **终端交互模式（Terminal）**：通过以下指令切换模式：
  ```bash
  mode normal/interact/expert
  ```

## 2. 样例数据集

- `AMLSim1K`（金融反洗钱图数据）

### 2.1 数据集概述

- **数据集名称**：AMLSim
- **类型**：反洗钱（AML）研究用合成图数据集（IBM Research）
- **图结构**：
  - 节点：账户（Account）
  - 边：交易（Transaction）
- **典型节点特征**：`Account_id`、`Init_balance`、`Account_type`、`Prior_sar_count`、`Open_dt`、`First_name`、`Last_name`、`City`、`State`、`Lat`、`Lon`
- **典型边特征**：`BASE_AMT`、`TRAN_TIMESTAMP`、`IS_SAR`
- **典型可疑模式**：
  - 资金聚合（Fan-in）
  - 回环交易（Cycle）

```bibtex
@misc{AMLSim, author = {Toyotaro Suzumura and Hiroki Kanezashi}, title = {{Anti-Money Laundering Datasets}: {InPlusLab} Anti-Money Laundering DataDatasets}, howpublished = {\url{http://github.com/IBM/AMLSim/}}, year = 2021 }
```

### 2.2 数据集选择

- **Web 界面**：在聊天界面中选择数据集「AMLSim1K」。
- **终端交互模式（Terminal）**：使用以下指令。  
  查看当前已配置的数据集：
  ```bash
  datasets
  ```
  选择数据集：
  ```bash
  use AMLSim1K
  ```
