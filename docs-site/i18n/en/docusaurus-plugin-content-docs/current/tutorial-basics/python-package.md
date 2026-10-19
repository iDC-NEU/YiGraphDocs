---
sidebar_position: 2
title: Python package
---

# YiGraph 2.0 Python package

The distribution is named `yigraph` and requires Python 3.11 or newer.
Use `import yigraph`; existing `aag.*` imports remain available.
The base package supports local graph analysis, the algorithm knowledge base and
the NetworkX MCP server. The `engine` extra installs the full AAG runtime;
`pyg` adds graph learning. The Next.js Web 2.0 frontend runs from the source checkout.

## Install and build

Use an isolated Python environment:

```bash
python -m venv .venv
# Linux / macOS
source .venv/bin/activate
# Windows PowerShell: .\.venv\Scripts\Activate.ps1
```

Version 2.0.0 is available on [PyPI](https://pypi.org/project/yigraph/2.0.0/):

```bash
python -m pip install yigraph==2.0.0
yigraph --version
```

Alternatively, install from the source checkout:

```bash
git clone --branch main https://github.com/iDC-NEU/YiGraph.git
cd YiGraph
python -m pip install .
yigraph --version
```

Use a revision containing `pyproject.toml`. For development, install with `-e .`.
Build distributable artifacts from the repository root:

```bash
python -m pip install build twine
python -m build
python -m twine check dist/*
python -m pip install ./dist/yigraph-2.0.0-py3-none-any.whl
```

The artifacts are `yigraph-2.0.0-py3-none-any.whl` and `yigraph-2.0.0.tar.gz`.
Use PyPI for a normal installation, or source/wheel installation for development and offline distribution.

## Run local graph analysis

```bash
yigraph demo
# Equivalent: python -m yigraph demo
```

This uses YiGraph's existing graph processor without an API key, GPU or database.
The shortest path from A to D is `["A", "C", "D"]`; D has the highest PageRank.
You can also call the processor directly:

```python
from yigraph import EdgeData, GraphComputationProcessor, VertexData

processor = GraphComputationProcessor()
processor.create_graph_from_edges(
    [VertexData(name) for name in ("A", "B", "C")],
    [EdgeData("A", "B"), EdgeData("B", "C")],
    directed=True,
)
print(processor.run_shortest_path("A", "C"))
print(processor.run_pagerank())
```

The checkout also includes `examples/graph_analysis.py`.

## Create a writable workspace

```bash
yigraph init ./yigraph-workspace
```

This copies credential-free templates under `config/`, the algorithm knowledge base
under `knowledge_base/`, and sample data and schemas under `datasets/`.
Existing files are preserved. Configure the workspace **before importing AAG modules**:

```python
import os
from pathlib import Path

os.environ["YIGRAPH_HOME"] = str(Path("./yigraph-workspace").resolve())
from yigraph import load_config

print(load_config().retrieval.embedding["device"])  # cpu
```

Alternatively, set `export YIGRAPH_HOME=/absolute/path/to/yigraph-workspace` on
Linux/macOS, or `$env:YIGRAPH_HOME = (Resolve-Path ./yigraph-workspace).Path` in PowerShell.
CLI commands accept `--home` instead. Without an override, a source checkout uses
its existing configuration, while a wheel installation uses `~/.yigraph`.
Initialize that default with `yigraph init ~/.yigraph` if desired.

## Run the AAG engine

Install the full engine from PyPI:

```bash
python -m pip install "yigraph[engine]==2.0.0"
# Optional graph learning:
python -m pip install "yigraph[engine,pyg]==2.0.0"
```

Or install extras from the checkout:

```bash
python -m pip install ".[engine]"
# Optional graph learning:
python -m pip install ".[engine,pyg]"
```

Or from your wheel:

```bash
python -m pip install "./dist/yigraph-2.0.0-py3-none-any.whl[engine]"
```

The engine extra downloads model, embedding and database client dependencies.
Select the PyTorch CPU/CUDA build for your hardware; the base package does not
force Linux CUDA packages onto other platforms.

Edit the workspace's `config/engine_config.yaml`:

- Set the model endpoint, API key and model under `reasoner.llm`, or select Ollama.
- Set the embedding model/device under `retrieval.embedding`; the template uses CPU.
- Configure the NebulaGraph, Milvus and Neo4j services required by your analysis.
- Review `config/config_servers.yaml`. The base package supports `networkx`;
  install `pyg` before enabling that server, or remove the unused `pyg` server entry.

Installing a Python package does not deploy databases or model services. The full
engine currently initializes retrieval modules, so prepare those services first.
A passing local graph demo alone does not verify the complete agent runtime.

With `YIGRAPH_HOME` set, call the asynchronous API:

```python
import asyncio
from yigraph import AAGEngine, load_config


async def main():
    engine = AAGEngine(load_config())
    try:
        print(engine.list_datasets())
        engine.specific_dataset("AMLSim1K", "graph")
        print(await engine.run("Find the ten most important accounts using PageRank.", mode="normal"))
    finally:
        await engine.shutdown()


asyncio.run(main())
```

Both `run()` and `shutdown()` require `await`. The `interact` and `expert` modes
return a DAG for review; edit it with `await engine.expert_modify_dag(...)` and
execute it with `await engine.expert_start_analysis()`.
See `examples/agent_analysis.py` for an executable script.
Start the existing terminal interaction with:

```bash
yigraph --home ./yigraph-workspace chat
```

Use `engine.list_datasets()` or the terminal `datasets` command to inspect sample
datasets. Database ingestion depends on the local service configuration.

## Web UI and documentation

The Flask backend and Next.js frontend remain under `web/frontend`; refer to
`web/README.md` for the source-based UI workflow.
To sync both language versions of this tutorial into a YiGraphDocs checkout:

```bash
python scripts/sync_python_docs.py ../YiGraphDocs
python scripts/sync_python_docs.py ../YiGraphDocs --check
```

Then run `npm ci` and `npm run build` inside `YiGraphDocs/docs-site`, and use the
existing documentation deployment workflow after the build passes.

If configuration cannot be found, initialize the workspace and set `YIGRAPH_HOME`
before imports. If the CLI is missing, activate the installation environment or use
`python -m yigraph`. MCP uses the current Python interpreter by default; a custom
server `command` must refer to an interpreter with YiGraph installed.

## Maintainers: publish to PyPI

Version 2.0.0 is already published and cannot be uploaded again or replaced.
Update the version before following these steps for a future release.

Register on [PyPI](https://pypi.org/), verify your email, enable two-factor
authentication, and create an API token. The initial release needs a token that
can create a new project. From the repository root:

```bash
python -m pip install ".[dev]"
python -m build
python scripts/publish_pypi.py --configure-token
python scripts/publish_pypi.py --check
python scripts/publish_pypi.py
```

The configuration command reads the token through a local hidden prompt and
stores it in the system credential manager, never in the repository. `--check`
does not upload. Publication validates the artifacts against the current project
metadata, uploads only that version's wheel and source archive, and rejects an
existing PyPI version. After publication, users can install with
`python -m pip install yigraph==2.0.0`. Bump the version before a later release.
