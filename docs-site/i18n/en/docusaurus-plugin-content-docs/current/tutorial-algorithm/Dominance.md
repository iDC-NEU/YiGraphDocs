---
sidebar_position: 7
---

# Dominance Operator Set

**Operator Category**: Dominance (Directed Graph Domination Relationships and Dominance Frontiers)

**Algorithm Count**: 2

**Applicable Stages**: Control flow analysis, process dependency analysis, program structure analysis, critical control node identification, path necessity relationship judgment, dominance frontier calculation

**Product Positioning**: Provides a unified directed graph domination relationship analysis capability base for answering "which nodes must be passed through from the entry node / who dominates whom / where does control flow merge / where does the influence range of variables or states end"

---

## 1. Operator Set Overview

The Dominance operator set primarily focuses on **domination relationship analysis in directed graphs**, commonly used in program control flow graphs, process flowcharts, state transition diagrams, and task dependency graphs.

In a directed graph, given an entry node `start`, if all paths from `start` to a node `v` must pass through node `u`, then `u` **dominates** `v`. This type of relationship helps answer:

1. **Essential Node Analysis**
   - Which nodes are essential from entry to target node?
   - Does a certain node control the reachable paths of subsequent nodes?
   - Typical algorithm: `immediate_dominators`

2. **Direct Domination Relationships**
   - Who is the nearest essential upstream node?
   - How to construct a domination tree?
   - Typical algorithm: `immediate_dominators`

3. **Control Flow Merge Boundaries**
   - Where does a node's influence range fork or merge?
   - Where does the influence boundary of variable definition, state change or control condition end?
   - Typical algorithm: `dominance_frontiers`

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operator | Function Description |
|---|---|---|
| Direct Domination Relationship | `immediate_dominators` | Calculate the immediate dominator of each node for constructing domination trees |
| Dominance Frontier | `dominance_frontiers` | Calculate the dominance frontier of each node to identify control flow merge points and influence range boundaries |

---

## 3. General Input and Output Conventions

### 3.1 Input

- **G**: Directed graph `DiGraph`
- **start**: Entry node / Starting node
  - Domination relationships are defined relative to the entry node
  - Only nodes reachable from `start` have clear domination relationship semantics

### 3.2 Output

- **immediate_dominators**: `dict[node, node]`
  - key: Node
  - value: The immediate dominator of that node
  - Usually `start`'s immediate dominator is itself

- **dominance_frontiers**: `dict[node, set[node]]`
  - key: Node
  - value: Set of dominance frontier nodes for that node

> Note: Domination relationships are primarily used for **directed graphs**. If the graph contains nodes unreachable from `start`, these nodes typically do not appear in the domination relationship results, or need to be handled separately at the business layer.

---

## 4. Detailed Operator Descriptions

### 1. immediate_dominators —— Immediate Dominator Nodes

**Function Description**
Calculate the immediate dominator of each reachable node when starting from entry node `start`.

If node `u` dominates node `v`, it means all paths from `start` to `v` must pass through `u`. The **immediate dominator** of `v` is the closest dominator among all nodes that dominate `v`.

**Product Value**
- Identify essential control points in processes
- Construct domination trees to understand process or program control structures
- Find the control scope of a node over subsequent nodes
- Determine whether certain steps must occur before other steps

**Typical Scenarios**
- Program control flow analysis: Determine essential relationships between basic blocks
- Compiler optimization: Construct domination trees for SSA conversion and optimization preparation
- Approval workflows: Identify critical approval nodes that all paths must pass through
- Task workflows: Determine whether a task is an essential step to reach subsequent tasks
- State machine analysis: Identify essential state nodes before entering a certain state

**Key Parameters**
- `G`: Directed graph
- `start`: Entry node

**Applicability and Characteristics**
- Graph Type: Directed graph
- Output: `dict[node, node]`
- Complexity: Usually near-linear level, suitable for control flow graphs and process flowcharts
- Note: Results have clear meaning only for nodes reachable from `start`

**Directly Answerable Questions**
- "Starting from the entry node, which nodes are essential to reach the target node?"
- "Who is the nearest essential upstream node for each node?"
- "What is the domination tree of this flowchart?"
- "Does a certain approval node dominate all subsequent critical nodes?"

---

### 2. dominance_frontiers —— Dominance Frontiers

**Function Description**
Calculate the dominance frontier of each node. For node `u`, its dominance frontier can be understood as: the position where `u`'s domination influence begins to "fail" or merge occurs.

More intuitively, if `u` dominates certain predecessor nodes on some paths but does not strictly dominate a merge node, then this merge node typically appears in `u`'s dominance frontier.

**Product Value**
- Identify control flow merge points
- Determine the boundary of variable, state or condition influence range
- Support φ function insertion position judgment in program SSA construction
- Help analyze where process branches reconverge

**Typical Scenarios**
- Compiler SSA construction: Determine φ function insertion positions
- Program control flow graphs: Identify merge nodes of if/else, loops and other structures
- Approval workflows: Identify positions where different approval paths reconverge
- State machine analysis: Find the boundary of state influence range
- Data flow analysis: Determine which merge points variable definitions affect

**Key Parameters**
- `G`: Directed graph
- `start`: Entry node

**Applicability and Characteristics**
- Graph Type: Directed graph
- Output: `dict[node, set[node]]`
- Usually calculated further based on immediate domination relationships
- Suitable for control flow, process flow, and merge structure analysis in dependency graphs

**Directly Answerable Questions**
- "Where is the influence boundary of a certain control condition?"
- "Where do process branches reconverge?"
- "Which nodes need to merge states from different paths?"
- "Which positions need φ function insertion in the control flow graph?"

---

## 5. Recommended Usage Guide (How to Choose)

- **Want to know who is the nearest essential upstream node for each node**: Use `immediate_dominators`
- **Want to construct a domination tree and analyze process control structure**: Use `immediate_dominators`
- **Want to know where branches or states merge**: Use `dominance_frontiers`
- **Want to do program control flow, SSA, variable definition influence range analysis**: First use `immediate_dominators`, then use `dominance_frontiers`
- **When the graph has multiple entry points**: Recommend adding a virtual entry node connecting all entries before calculating domination relationships
- **When the graph has unreachable nodes**: Recommend filtering reachable subgraph from `start` first, then analyzing domination relationships

---

## 6. Typical Directly Answerable Questions

- "Starting from entry node S, who is the immediate dominator of each node?"
- "Must all paths from node S to target node T pass through node A?"
- "What is the domination tree of this flowchart?"
- "Which nodes are critical essential control points in the process?"
- "Where does the influence range of a certain conditional branch end?"
- "Which nodes in the process are merge points of multiple branches?"
- "Which positions need φ function insertion in the control flow graph?"
- "How to uniformly analyze domination relationships in a multi-entry flowchart?"

---

## 7. Engineering Implementation Notes

1. **Domination relationships must specify entry node**
   - Domination relationships are not absolute graph properties, but defined relative to `start`.
   - Different entry nodes may produce completely different domination trees and dominance frontiers.

2. **Only analyze nodes reachable from entry**
   - Nodes unreachable from `start` do not have clear domination semantics.
   - In engineering, it is recommended to first filter the reachable subgraph or separately mark unreachable nodes.

3. **Multiple entries recommend adding virtual entry**
   - If the process has multiple starting points, you can add `virtual_start`.
   - Point from `virtual_start` to all real entries, then calculate domination relationships.

4. **Domination is not equivalent to parent-child relationships**
   - Domination relationships mean "all paths must pass through".
   - It is not necessarily equivalent to direct edge relationships in the original graph, nor equivalent to business-level parent-child relationships.

5. **Dominance frontier is suitable for analyzing merge points**
   - `dominance_frontiers` is commonly used to identify branch merges, state merges, and variable merge positions.
   - In ordinary business processes, it can be interpreted as "the position where a control node's influence begins to lose unique control".

6. **Cyclic graphs can also be analyzed, but interpretation needs care**
   - Control flow graphs typically contain loops, and domination algorithms can handle such structures.
   - However, business interpretation needs to distinguish between loop entry, back edges and ordinary process edges.

7. **Suitable for combining with DAG / Path / Connectivity algorithms**
   - Can first use reachability or connectivity algorithms to confirm entry reachable range.
   - Then use Dominance to analyze essential nodes and merge boundaries.
   - For flowcharts, can also combine with topological, cycle detection, strongly connected components and other capabilities for structure diagnosis.

---

## 8. Operator Checklist

| No. | Operator Name | Description |
|---:|---|---|
| 1 | `immediate_dominators` | Immediate Dominator Node Calculation |
| 2 | `dominance_frontiers` | Dominance Frontier Calculation |