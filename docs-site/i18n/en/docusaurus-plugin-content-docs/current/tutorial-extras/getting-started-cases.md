---
sidebar_position: 0
---

# User Onboarding Guide

## 1. Introduction

This document helps new users quickly understand the typical usage of **EtuGraph** on graph datasets, and complete various tasks from basic analysis to complex decision-making with the help of three modes: **Normal / Interactive / Expert**.

### Explanation of the Three Modes

- **Normal Mode**: Users only need to submit business questions, and EtuGraph will automatically complete question parsing, algorithm selection, calculation execution, and generate an analysis report. This mode is suitable for non-technical users or general business users.
- **Interactive Mode**: Users collaborate with EtuGraph to analyze business questions. For a given question, EtuGraph will interact with a large model to determine the calculation process and graph algorithms, then execute according to the established plan and feedback the analysis report. This mode is suitable for advanced users who have a certain understanding of business and graph algorithms.
- **Expert Mode**: Users directly provide business questions, as well as solutions, calculation steps and graph algorithms. EtuGraph executes calculations according to the user's plan and outputs an analysis report. This mode is suitable for expert users who have an in-depth grasp of business and graph algorithms.

### Usage Methods

- **Web Interface**: The chat interface defaults to Normal Mode, and you can select 「Interactive Mode」 or 「Expert Mode」 in the interface.
- **Terminal Interaction Mode**: Switch modes through the following command:
  ```bash
  mode normal/interact/expert
  ```

## 2. Sample Dataset

- `AMLSim1K` (Financial Anti-Money Laundering Graph Data)

### 2.1 Dataset Overview

- **Dataset Name**: AMLSim
- **Type**: Synthetic graph dataset for Anti-Money Laundering (AML) research (IBM Research)
- **Graph Structure**:
  - Nodes: Account
  - Edges: Transaction
- **Typical Node Features**: `Account_id`, `Init_balance`, `Account_type`, `Prior_sar_count`, `Open_dt`, `First_name`, `Last_name`, `City`, `State`, `Lat`, `Lon`
- **Typical Edge Features**: `BASE_AMT`, `TRAN_TIMESTAMP`, `IS_SAR`
- **Typical Suspicious Patterns**:
  - Fan-in (Fund Aggregation)
  - Cycle (Loop Transaction)

```bibtex
@misc{AMLSim, author = {Toyotaro Suzumura and Hiroki Kanezashi}, title = {{Anti-Money Laundering Datasets}: {InPlusLab} Anti-Money Laundering DataDatasets}, howpublished = {\url{http://github.com/IBM/AMLSim/}}, year = 2021 }
```

### 2.2 Dataset Selection

- **Web Interface**: Select the dataset 「AMLSim1K」 in the chat interface.
- **Terminal Interaction Mode**: Use the following commands.
  View currently configured datasets:
  ```bash
  datasets
  ```
  Select a dataset:
  ```bash
  use AMLSim1K
  ```

