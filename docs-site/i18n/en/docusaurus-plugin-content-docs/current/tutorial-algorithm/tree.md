---
sidebar_position: 6
---
# Tree & Spanning Tree Operators

**Operator Category**: Tree & Spanning Tree (tree structure analysis, spanning trees, chordal graphs, treewidth estimation)

**Number of Algorithms**: 9

**Applicable Stages**: network backbone extraction, minimum cost connection, maximum benefit connection, random backbone sampling, tree structure query, hierarchical relationship analysis, broadcast center identification, chordal graph completion, treewidth estimation and graph structural complexity assessment

**Product Positioning**: Provide a unified tree and spanning tree capability base for answering questions such as: "How to extract an acyclic backbone covering all nodes?", "How to connect all nodes with minimum cost or maximum benefit?", "How to randomly generate alternative backbones?", "What is the lowest common ancestor of two nodes in a tree?", "Where is the best starting point for broadcasting?", "Can the graph be completed to a chordal graph?", "How complex is the graph structure?"

---

## I. Operator Set Overview

The Tree & Spanning Tree operator set addresses undirected graphs, trees, DAGs/hierarchical structures, and graph structural complexity, covering the following core problems:

1. **Spanning Trees & Spanning Forests**
   - How to connect all nodes with minimum cost?
   - How to extract a backbone with maximum benefit, similarity, or bandwidth?
   - How to randomly sample spanning trees for simulation, alternative planning, or generative structures?
   - Typical algorithms: `minimum_spanning_tree`, `maximum_spanning_tree`, `random_spanning_tree`

2. **Tree Structure Queries & Hierarchical Relationship Analysis**
   - In a tree or DAG, what is the nearest common upstream node of two nodes?
   - How to determine common parent, common ancestor, or meeting point in a hierarchy?
   - Typical algorithm: `lowest_common_ancestor`

3. **Broadcast Center on Trees**
   - If information starts broadcasting from a node, which node as the source yields optimal propagation?
   - Which node is the most suitable broadcast center or diffusion center in a tree?
   - Typical algorithm: `tree_broadcast_center`

4. **Chordal Graphs & Graph Completion**
   - How to complete a graph to a chordal graph?
   - After completion, how to support more efficient inference, decomposition, or tree decomposition approximation?
   - Typical algorithm: `complete_to_chordal_graph`

5. **Treewidth Estimation & Structural Complexity Assessment**
   - How far is the graph from being tree-like?
   - Is the graph's structural complexity suitable for dynamic programming, decomposition solving, or approximate inference?
   - Typical algorithms: `chordal_graph_treewidth`, `treewidth_min_fill_in`, `treewidth_min_degree`

> Note: When the input graph `G` is **disconnected**, spanning tree algorithms typically return a **spanning forest**, i.e., one spanning tree per connected component.

---

## II. Operator Capability Classification

| Capability Type                     | Operator                                | Description                                                                 |
|-------------------------------------|-----------------------------------------|-----------------------------------------------------------------------------|
| Minimum cost backbone               | `minimum_spanning_tree`                 | Builds a spanning tree/forest covering all nodes with minimum total edge weight |
| Maximum benefit backbone            | `maximum_spanning_tree`                 | Builds a spanning tree/forest covering all nodes with maximum total edge weight |
| Random backbone sampling            | `random_spanning_tree`                  | Randomly samples a spanning tree according to weight distribution           |
| Lowest common ancestor              | `lowest_common_ancestor`                | Queries the LCA of two nodes in a tree or DAG                               |
| Tree broadcast center               | `tree_broadcast_center`                 | Finds the optimal broadcast source node(s) in a tree                        |
| Chordal graph completion            | `complete_to_chordal_graph`             | Completes a graph to a chordal graph, returns completion and elimination order |
| Chordal graph treewidth             | `chordal_graph_treewidth`               | Computes the treewidth of a chordal graph                                   |
| Treewidth approximation (min fill-in) | `treewidth_min_fill_in`               | Estimates treewidth and builds tree decomposition using min-fill heuristic  |
| Treewidth approximation (min degree) | `treewidth_min_degree`                 | Estimates treewidth and builds tree decomposition using min-degree heuristic|

---

## III. General Input/Output Conventions

### 3.1 Input

- **G**: NetworkX graph object  
  - Spanning tree algorithms are typically for undirected graphs (`Graph`).
  - Lowest common ancestor is typically for trees, DAGs, or directed hierarchical structures.
  - Chordal graph and treewidth algorithms are typically for undirected graphs.

- **weight**: edge weight attribute name, commonly defaulting to `"weight"`.

- **algorithm**: spanning tree algorithm choice: `kruskal`, `prim`, or `boruvka`.

- **seed**: random seed for stochastic algorithms like random spanning tree, to ensure reproducibility.

### 3.2 Output

- **Spanning tree algorithms**: `NetworkX Graph` representing a spanning tree or spanning forest.
- **Lowest common ancestor**: node ID or a mapping from node pairs to LCA.
- **Broadcast center**: set of center node(s) or result including center and broadcast time.
- **Chordal graph completion**: completed graph along with elimination order / mapping information.
- **Treewidth algorithms**: treewidth estimate and tree decomposition structure.

---

## IV. Detailed Operator Descriptions

### 1. minimum_spanning_tree — Minimum Spanning Tree (MST)

**Description**  
Selects a set of edges in a weighted undirected graph such that:

1. All nodes are covered;
2. No cycles are formed;
3. The total edge weight is minimized.

When the graph is disconnected, returns a minimum spanning tree for each component, i.e., a minimum spanning forest.

**Product Value**
- Extracts a network backbone with minimum cost.
- Preserves node coverage while removing redundant edges.
- Suitable for network planning, cost optimization, and structural simplification.

**Typical Scenarios**
- Infrastructure (fiber, water, power) covering all sites at minimum cost.
- Logistics: extracting a skeleton with minimum transport cost.
- Road, communication, supply chain networks: minimum‑cost connection plan.
- Complex network visualization: keeping the lowest‑cost backbone.

**Key Parameters**
- `weight`: edge weight attribute, e.g., `cost`, `distance`, `latency`.
- `algorithm`: `kruskal` / `prim` / `boruvka`.
- `ignore_nan`: whether to ignore edges with NaN weight.

**Applicability & Characteristics**
- Graph type: undirected; supports multigraphs.
- Output: spanning tree or forest.
- Complexity: typical implementation `O(E log V)`.

---

### 2. maximum_spanning_tree — Maximum Spanning Tree (MaxST)

**Description**  
Selects a set of edges in a weighted undirected graph covering all nodes without cycles, maximizing the total edge weight.

**Product Value**
- Extracts a backbone with maximum benefit, bandwidth, or similarity.
- Suitable for retaining the most important connections in high‑strength networks.
- Can be used for structural simplification, core relation extraction, and high‑value connection modeling.

**Typical Scenarios**
- Communication networks: extract a high‑bandwidth backbone.
- Similarity networks: keep the maximum‑similarity backbone for visualization.
- Trust networks: extract the most trustworthy propagation backbone.
- Recommendation networks: retain the strongest user‑content associations.

**Key Parameters**
- `weight`: edge weight attribute, e.g., `bandwidth`, `similarity`, `trust`.
- `algorithm`: `kruskal` / `prim` / `boruvka`.
- `ignore_nan`: whether to ignore edges with NaN weight.

**Applicability & Characteristics**
- Graph type: undirected; supports multigraphs.
- Output: spanning tree or forest.
- Complexity: typical implementation `O(E log V)`.
- Note: When many edges share the same weight, multiple equivalent maximum spanning trees may exist.

---

### 3. random_spanning_tree — Random Spanning Tree Sampling

**Description**  
Randomly samples a spanning tree from the given undirected graph according to a specified probability distribution. Edge weights can influence sampling probabilities.

**Product Value**
- Supports randomized backbone generation.
- Useful for simulation, stress testing, alternative planning, and generative structures.
- Avoids always outputting the same deterministic minimum or maximum backbone.

**Typical Scenarios**
- Maze generation: create acyclic, fully connected random paths.
- Network stress testing: randomly sample temporary backbone structures.
- Resilience simulation: sample many spanning trees to count edge inclusion frequencies.
- Path or topology generation: produce multiple alternative connection plans.

**Key Parameters**
- `weight`: sampling weight field; `None` means uniform sampling.
- `multiplicative`: controls whether tree probability depends on product or sum of weights.
- `seed`: random seed for reproducibility.

**Applicability & Characteristics**
- Graph type: undirected.
- Output: a random spanning tree.
- Note: When the input graph is disconnected, check the implementation’s behavior (error or spanning forest).

---

### 4. lowest_common_ancestor — Lowest Common Ancestor

**Description**  
In a tree, DAG, or directed hierarchical structure, returns the lowest common ancestor of two nodes. The LCA is the deepest node that is an ancestor of both nodes.

**Product Value**
- Enables query of common parent / shared ancestor in hierarchies.
- Useful for organizational charts, classification systems, dependency trees, knowledge hierarchies.
- Helps locate the nearest meeting point or common source of two nodes.

**Typical Scenarios**
- Organization: who is the nearest common manager of two employees?
- Classification: what is the nearest common parent category of two products?
- File system: what is the nearest common directory of two files?
- Dependency analysis: what is the nearest common dependency source of two modules?
- Knowledge graph: what is the nearest common hypernym of two concepts?

**Key Parameters**
- `node1` / `node2`: the two nodes to query.
- `default`: default return value when no common ancestor exists.
- `pairs`: batch query for multiple node pairs.

**Applicability & Characteristics**
- Graph type: tree / DAG / directed hierarchical graph.
- Output: node or mapping from node pairs to LCA.
- Note: If the graph is not a tree or DAG, verify structural semantics before applying LCA.

---

### 5. tree_broadcast_center — Tree Broadcast Center

**Description**  
In a tree, finds the node(s) most suitable as broadcast source such that information propagation from that node to the whole tree achieves good broadcast efficiency (e.g., minimal eccentricity).

**Product Value**
- Identifies the best propagation starting point in a tree.
- Supports center selection in hierarchical organizations, communication trees, distribution trees.
- Can reduce broadcast time, propagation levels, or optimize notification paths.

**Typical Scenarios**
- Organization notification: which department or role starts notifications fastest?
- Communication network: choose the broadcast source in a tree topology.
- Content distribution: select the starting node in a tree distribution structure.
- Task assignment: best starting point in a hierarchical task network.

**Applicability & Characteristics**
- Graph type: tree.
- Output: broadcast center node(s) or related information.
- Note: Suitable for acyclic connected structures; if input is not a tree, extract a spanning tree first.

---

### 6. complete_to_chordal_graph — Chordal Graph Completion

**Description**  
Completes an undirected graph to a chordal graph. A chordal graph requires that every cycle of length greater than 3 has a chord.

**Product Value**
- Transforms a general graph into a structure more amenable to decomposition and inference.
- Supports subsequent tree decomposition, variable elimination, probabilistic graphical model inference, etc.
- Helps reduce the difficulty of solving complex graph problems.

**Typical Scenarios**
- Probabilistic graphical models: triangulation before variable elimination.
- Knowledge graph structure decomposition.
- Constraint network solving.
- Structural simplification and approximate inference in complex networks.
- Query optimization preprocessing in graph databases.

**Applicability & Characteristics**
- Graph type: undirected.
- Output: chordal graph completion result and associated elimination order / mapping.
- Note: Completion adds edges; added edges represent algorithmic structural filling, not necessarily real business relationships.

---

### 7. chordal_graph_treewidth — Chordal Graph Treewidth

**Description**  
Computes the treewidth of a chordal graph. Treewidth measures how far a graph is from being a tree – smaller treewidth means the graph is more tree‑like, and many graph algorithms become easier.

**Product Value**
- Quantifies graph structural complexity.
- Determines whether the graph is suitable for tree decomposition, dynamic programming, or exact inference.
- Can compare structural difficulty across different networks.

**Typical Scenarios**
- Complexity estimation for probabilistic graphical model inference.
- Difficulty assessment for constraint satisfaction problems.
- Structural complexity analysis in query optimization.
- Determining whether a complex network is close to a tree structure.

**Applicability & Characteristics**
- Graph type: chordal graph.
- Output: treewidth value.
- Note: For general graphs, perform chordal graph completion first or use treewidth approximation algorithms.

---

### 8. treewidth_min_fill_in — Treewidth Approximation (Minimum Fill‑In Heuristic)

**Description**  
Estimates the treewidth of a general graph using the minimum fill‑in heuristic and produces a corresponding tree decomposition. This heuristic tends to eliminate nodes that introduce few fill edges.

**Product Value**
- Approximates structural complexity of general graphs.
- Produces a tree decomposition usable for dynamic programming, inference, and decomposition‑based solving.
- More suitable for engineering scenarios than exact treewidth computation.

**Typical Scenarios**
- Approximate inference in probabilistic graphical models.
- Complexity assessment before solving constraint networks.
- Graph query plan optimization.
- Decomposing large graphs into smaller subproblems.
- Tree‑like approximation analysis of complex dependency networks.

**Applicability & Characteristics**
- Graph type: undirected.
- Output: treewidth estimate and tree decomposition.
- Note: Heuristic does not guarantee optimal treewidth but is usually practically useful.

---

### 9. treewidth_min_degree — Treewidth Approximation (Minimum Degree Heuristic)

**Description**  
Estimates the treewidth of a general graph using the minimum degree heuristic and produces a corresponding tree decomposition. This heuristic eliminates nodes with the smallest current degree.

**Product Value**
- Quickly estimates graph treewidth.
- Suitable for scenarios where a low‑cost structural complexity metric is needed.
- Can serve as a baseline or alternative to the minimum fill‑in heuristic.

**Typical Scenarios**
- Rapid structural complexity evaluation of large graphs.
- Feasibility judgment before executing graph algorithms.
- Preliminary analysis for constraint networks and probabilistic graphical models.
- Optimization preprocessing for graph queries and graph computing tasks.

**Applicability & Characteristics**
- Graph type: undirected.
- Output: treewidth estimate and tree decomposition.
- Strengths: usually faster.
- Note: Results are heuristic and not optimal; compare with `treewidth_min_fill_in` when needed.

---

## V. Recommended Usage Guide (Quick Selection)

- **Goal: minimum cost / shortest distance / lowest latency backbone** → use `minimum_spanning_tree`
- **Goal: maximum bandwidth / maximum trust / maximum similarity backbone** → use `maximum_spanning_tree`
- **Goal: randomization, simulation, sampling, multiple alternatives** → use `random_spanning_tree`
- **Goal: query common upstream node of two nodes in tree or DAG** → use `lowest_common_ancestor`
- **Goal: find best broadcast start node in a tree** → use `tree_broadcast_center`
- **Goal: convert general graph to a structure suitable for decomposition and inference** → use `complete_to_chordal_graph`
- **Goal: compute treewidth of a chordal graph** → use `chordal_graph_treewidth`
- **Goal: approximate treewidth of a general graph, preferring minimal fill‑in** → use `treewidth_min_fill_in`
- **Goal: fast approximate treewidth of a general graph** → use `treewidth_min_degree`

---

## VI. Typical Questions That Can Be Answered Directly (Examples)

- “Compute the minimum spanning tree of this weighted undirected graph, and output the total weight and all edges.”
- “In a bandwidth network, extract the maximum spanning tree as the high‑capacity backbone.”
- “Randomly sample 10 spanning trees and count the frequency of each edge.”
- “If the input graph is disconnected, output the spanning forest for each component.”
- “What is the nearest common manager of two organizational nodes?”
- “What is the nearest common parent category of two product classes?”
- “In a tree communication network, which node is the best broadcast source?”
- “Complete this graph to a chordal graph and indicate which edges were added.”
- “What is the treewidth of this chordal graph?”
- “Use the minimum fill‑in heuristic to estimate the treewidth of this graph and output the tree decomposition.”
- “Use the minimum degree heuristic to quickly assess whether this graph is close to a tree.”

---

## VII. Engineering Implementation Considerations

1. **Edge weight semantics must align**
   - For cost, distance, latency → use `minimum_spanning_tree`.
   - For bandwidth, similarity, trust, benefit → use `maximum_spanning_tree`.
   - Do not mix “smaller is better” with “larger is better” semantics.

2. **Disconnected input graphs yield spanning forests**
   - Minimum/maximum spanning tree on a disconnected graph typically does not output a single tree.
   - Results should be interpreted as a forest, with one tree per component.

3. **Multigraphs require attention to edge keys**
   - Multiple edges between two nodes exist.
   - The resulting spanning tree may pick one specific edge; interpret using its key or attributes.

4. **NaN weights should be pre‑processed**
   - MST / MaxST may raise errors on NaN weights.
   - Clean weights beforehand or use `ignore_nan` to skip invalid edges.

5. **Equal weights may lead to multiple solutions**
   - When multiple edges share the same weight, spanning trees may not be unique.
   - Different algorithms, orders, or random seeds may output different but equivalent trees.

6. **Confirm structure semantics before LCA queries**
   - `lowest_common_ancestor` is suitable for trees, DAGs, or explicit hierarchies.
   - If the graph has complex cycles, validate or transform the structure first; otherwise “ancestor” may be ambiguous.

7. **Added edges in chordal graph completion are not real relationships**
   - Edges added by `complete_to_chordal_graph` are algorithmic fill‑ins.
   - In business presentation, distinguish original edges from completion edges to avoid misunderstanding.

8. **Treewidth approximations are not exact optimal**
   - `treewidth_min_fill_in` and `treewidth_min_degree` are heuristics.
   - Suitable for engineering estimation and preprocessing; do not treat them as exact optimal treewidth.
   - For critical cases, compare results from both heuristics and verify against scale.

9. **Smaller treewidth is better for tree‑decomposition methods**
   - Low treewidth graphs are more suitable for dynamic programming, exact inference, constraint solving, and query optimization.
   - High treewidth usually implies more complex structure and significantly higher algorithm costs.

---

## VIII. Operator List

| No. | Operator Name                        | Description (Chinese)                      |
|-----|--------------------------------------|--------------------------------------------|
| 1   | `minimum_spanning_tree`              | Minimum spanning tree / forest             |
| 2   | `maximum_spanning_tree`              | Maximum spanning tree / forest             |
| 3   | `random_spanning_tree`               | Random spanning tree sampling              |
| 4   | `lowest_common_ancestor`             | Lowest common ancestor                     |
| 5   | `tree_broadcast_center`              | Tree broadcast center                      |
| 6   | `complete_to_chordal_graph`          | Chordal graph completion                   |
| 7   | `chordal_graph_treewidth`            | Chordal graph treewidth                    |
| 8   | `treewidth_min_fill_in`              | Minimum fill‑in heuristic treewidth        |
| 9   | `treewidth_min_degree`               | Minimum degree heuristic treewidth         |