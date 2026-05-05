---
sidebar_position: 15
title: Graph Query Operator Set
---

# Graph Query Operator Set

**Operator Category**: Graph Query (structural retrieval and subgraph operations)

**Number of Algorithms**: 10

**Applicable Stages**: Node attribute query, neighbor expansion, common neighbor discovery, path retrieval, subgraph extraction, graph structure partitioning, graph difference measurement, quotient graph construction, conditional independence testing, graph merging and structural concatenation

**Product Positioning**: Provides foundational graph query and structural manipulation capabilities for "querying node attributes / k-hop neighbors / common neighbors / paths between two nodes / extracting subgraphs / partitioning nodes by nearest center / comparing graph differences / compressing graph structures / testing d-separation / merging graph structures".

---

## I. Operator Set Overview

The Graph Query operator set focuses on **graph data retrieval, local structure extraction, path querying, structure partitioning, and graph-level operations**, addressing the following key questions:

1. **Node and Attribute Query**
   - What attributes does a given node have?
   - Can business fields, labels, or metadata be retrieved in batch for nodes?
   - How can node attribute information be provided for subsequent filtering, visualization, or analysis?

2. **Neighbor and Local Structure Query**
   - Which nodes are within k hops of a given node?
   - Do two nodes share common neighbors?
   - How can local relationship circles or potential associated objects be quickly identified?

3. **Path and Relationship Chain Query**
   - Does a path exist between two nodes?
   - What are the reachable paths between two nodes?
   - How can the association chain between two entities be explained?

4. **Subgraph Extraction and Structure Partitioning**
   - How can a subgraph consisting of specified nodes or edges be extracted from the original graph?
   - How can a graph be partitioned into different Voronoi regions based on a set of center nodes?
   - How can a graph be compressed into a quotient graph according to node groupings?

5. **Graph Structure Comparison, Reasoning, and Merging**
   - How large is the edit distance between two graphs?
   - In directed probabilistic graphs or causal graphs, are certain nodes d-separated?
   - How can two graphs be merged via a full join to form a combined structure?

---

## II. Operator Capability Classification

| Capability Type               | Corresponding Operator            | Description                                                              |
|-------------------------------|------------------------------------|--------------------------------------------------------------------------|
| Node Attribute Query          | `get_node_attributes`              | Retrieve specified attributes of nodes in the graph.                     |
| K-hop Neighbor Query          | `k_hop_neighbors`                  | Query neighbors within k hops of a given node.                          |
| Common Neighbor Query         | `common_neighbors`                 | Query common neighbors of two nodes.                                    |
| Subgraph Extraction           | `extract_subgraph`                 | Extract a subgraph based on node or edge conditions.                    |
| Path Query Between Two Nodes  | `get_paths_between_two_nodes`      | Query paths between two nodes.                                          |
| Voronoi Partition             | `voronoi_cells`                    | Partition the graph into nearest-node regions around center nodes.      |
| Graph Edit Distance           | `graph_edit_distance`              | Measure the edit distance between two graphs.                           |
| Quotient Graph Construction   | `quotient_graph`                   | Compress graph structure based on node groupings or equivalence.        |
| d-separation Test             | `is_d_separator`                   | Determine whether a node set d-separates two other sets in a DAG.       |
| Graph Full Join               | `full_join`                        | Merge two graphs via a full join operation.                             |

---

## III. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph
- **Common Input Parameters**:
  - `node` / `source` / `target`: Query start, end, or target node
  - `nodes`: Node set for attribute query, subgraph extraction, or grouping
  - `attribute` / `name`: Node attribute name
  - `k` / `cutoff`: Hop count or path search depth limit
  - `center_nodes`: Set of center nodes for Voronoi partitioning
  - `partition`: Node grouping for quotient graph construction
  - `X` / `Y` / `Z`: Node sets for d-separation test
  - `G1` / `G2`: Two input graphs for comparison or merging
- **Common Output Types**:
  - Attribute query: `dict[node → attribute_value]`
  - Neighbor query: `set(node)` or list of nodes
  - Path query: list of paths
  - Subgraph: `NetworkX Graph`
  - Structure partition: `dict[center_node → set(nodes)]`
  - Graph difference: numeric distance
  - Boolean test: `bool`
  - Graph merge: merged `NetworkX Graph`

---

## IV. Detailed Operator Descriptions

### 1. get_node_attributes – Node Attribute Query

**Description**  
Retrieves specified attributes of nodes in the graph and returns a mapping from nodes to attribute values.

**Product Value**
- Quickly read node business fields
- Support node labeling, coloring, sizing for graph visualization
- Serve as basic input for filtering, grouping, statistics, and subsequent algorithmic analysis

**Typical Scenarios**
- Query risk level of account nodes
- Query age, region, or tags of user nodes
- Query year, field, or author information of paper nodes
- Set node colors or groups for visualization
- Extract node metadata before graph analysis

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Graph `G`, attribute name
- Output: `dict[node → attribute_value]`
- Features: Data‑reading oriented, suitable for graph query and pre‑visualization processing

---

### 2. k_hop_neighbors – K‑Hop Neighbor Query

**Description**  
Starting from a given node, returns the set of nodes reachable within k hops.

**Product Value**
- Quickly expand the relationship circle around a node
- Support local influence scope, relationship diffusion, and discovery of potential associated objects
- Essential precursor for local graph exploration and subgraph extraction

**Typical Scenarios**
- Query 2‑hop trading counterparties of an account
- Query friends and friends‑of‑friends of a user
- Query dependent nodes around a device
- Query concept nodes near an entity in a knowledge graph
- Query associated objects around a risky node

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Start node, hop count `k`
- Output: Set of neighbor nodes
- Note: High‑order k‑hop queries can expand rapidly; control hop count and result size.

---

### 3. common_neighbors – Common Neighbor Query

**Description**  
Returns the set of neighbors shared by two nodes.

**Product Value**
- Discover common relationship basis between two entities
- Support friend recommendation, similarity analysis, and hidden relationship discovery
- Serve as basic features for link prediction and relationship strength assessment

**Typical Scenarios**
- Query common friends of two persons
- Query common trading counterparties of two accounts
- Query shared suppliers or customers of two companies
- Query co‑cited literature of two papers
- Query whether two risky objects have a common intermediary

**Applicability & Characteristics**
- Graph type: Typically undirected
- Input: Two nodes `u`, `v`
- Output: Set of common neighbor nodes
- Features: Suitable for local structure query and relationship explanation

---

### 4. extract_subgraph – Subgraph Extraction

**Description**  
Extracts a new subgraph from the original graph based on specified nodes, edges, or conditions.

**Product Value**
- Break a large graph into smaller, analyzable and visualizable pieces
- Support local structure analysis, visual data sampling, and preprocessing for algorithms
- Facilitate precise inspection around target objects

**Typical Scenarios**
- Extract a transaction subgraph around suspicious accounts
- Extract internal relationships among a set of nodes
- Extract a subgraph corresponding to a community or group
- Extract a topic fragment from a knowledge graph
- Prepare local graphs for path analysis, community detection, or visualization

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Node set, edge set, or extraction condition
- Output: `NetworkX Graph`
- Features: Suitable for cutting business‑relevant structures from large graphs

---

### 5. get_paths_between_two_nodes – Path Query Between Two Nodes

**Description**  
Queries paths between two nodes to determine reachability and the intermediate nodes connecting them.

**Product Value**
- Explain the relationship chain between two nodes
- Support path tracing in fund chains, citation chains, supply chains, social chains, etc.
- Help discover indirect associations and hidden relationships

**Typical Scenarios**
- Query whether a fund path exists between two accounts
- Query the supply chain path between two companies
- Query the social connection chain between two users
- Query the citation path between two papers
- Query intermediate nodes linking risky objects

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Start node, end node, path length limit
- Output: List of paths
- Note: Enumerating all paths may cause combinatorial explosion; set max path length or result count limits.

---

### 6. voronoi_cells – Voronoi Node Partitioning

**Description**  
Given a set of center nodes, assigns every other node in the graph to the nearest center node, forming graph Voronoi cells.

**Product Value**
- Support node‑to‑nearest‑center assignment
- Enable analysis of service coverage, influence regions, and nearest belonging
- Suitable for spatially or topologically partitioning a graph by multiple centers

**Typical Scenarios**
- Partition coverage areas of multiple service centers
- Analyze influence regions of multiple core nodes
- Partition road networks by nearest stations
- Partition social networks by core users
- Partition knowledge graph entities by topic centers

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Set of center nodes
- Output: `dict[center_node → set(nodes)]`
- Features: Region partitioning based on graph distance

---

### 7. graph_edit_distance – Graph Edit Distance

**Description**  
Computes the edit distance between two graphs – the cost of transforming one graph into the other via node/edge insertions, deletions, or substitutions.

**Product Value**
- Measure structural differences between two graphs
- Support graph structure similarity comparison and pattern matching
- Enable anomaly structure detection, template matching, and graph clustering

**Typical Scenarios**
- Compare similarity between two flowcharts
- Determine whether two fraud group structures are similar
- Compare differences between two molecular structures
- Detect anomalous changes in network structure
- Compute similarity between graph samples

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Two graphs `G1`, `G2`
- Output: Numeric edit distance
- Note: Graph edit distance is computationally expensive; use with care on large graphs.

---

### 8. quotient_graph – Quotient Graph Construction

**Description**  
Compresses the original graph into a quotient graph based on node groupings, equivalence relations, or partitions. Each node in the quotient graph typically represents a set of nodes in the original graph.

**Product Value**
- Compress a complex graph into a higher‑level structure
- Support community‑level, group‑level, or module‑level relationship analysis
- Facilitate macroscopic observation of graph structure

**Typical Scenarios**
- Compress communities into a community relationship graph
- Compress department members into a department relationship graph
- Compress a city road network into a regional relationship graph
- Generate a topic graph after grouping knowledge graph entities
- Produce structural summaries and hierarchical models of large graphs

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Node partition or equivalence relation
- Output: `NetworkX Graph` (quotient graph)
- Features: Suitable for graph compression, structural summarization, and macro‑level analysis

---

### 9. is_d_separator – d‑separation Test

**Description**  
Determines whether node set `Z` d‑separates node set `X` from node set `Y` in a directed graph. Commonly used in directed probabilistic graphs, Bayesian networks, and causal graphs for conditional independence testing.

**Product Value**
- Support conditional independence reasoning on graph structures
- Enable causal analysis and interpretation of probabilistic graphical models
- Help determine whether a given conditioning set blocks dependency paths between two variable sets

**Typical Scenarios**
- Conditional independence testing in Bayesian networks
- Confounder analysis in causal graphs
- Determine whether control variables block causal paths
- Validate probabilistic graphical model structures
- Explain variable dependency relationships

**Applicability & Characteristics**
- Graph type: Typically directed acyclic graphs (DAGs)
- Input: Node sets `X`, `Y`, `Z`
- Output: `bool`
- Features: Geared toward graph reasoning and structural conditional independence analysis

---

### 10. full_join – Graph Full Join

**Description**  
Performs a full join operation on two graphs, producing a combined structure containing nodes and edges from both graphs, with optional connections between nodes of the two graphs.

**Product Value**
- Support structural merging and concatenation of graphs
- Enable construction of combined graphs, cross‑graph relationship graphs, or experimental graph structures
- Suitable for scenarios that require bringing two independent graphs into a single analysis space

**Typical Scenarios**
- Merge relationship networks from two sources
- Build a joint graph across business domains
- Combine a user graph and a product graph for joint analysis
- Combine two subgraphs for structural experiments
- Construct an overall network with cross‑graph connections

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Input: Two graphs `G1`, `G2`
- Output: Merged graph
- Features: Suitable for graph concatenation, joint analysis, and cross‑domain modeling

---

## V. Recommended Usage Guide

### 1. Node Attribute & Local Query

- Query node attributes: `get_node_attributes`
- Query k‑hop neighbors: `k_hop_neighbors`
- Query common neighbors: `common_neighbors`

### 2. Path & Relationship Chain Analysis

- Query paths between two nodes: `get_paths_between_two_nodes`
- Determine indirect association: `get_paths_between_two_nodes`
- Discover shared intermediaries or common objects: `common_neighbors`

### 3. Subgraph Extraction & Local Analysis

- Extract subgraph defined by nodes/edges: `extract_subgraph`
- Extract local relationship circles: `extract_subgraph` + `k_hop_neighbors`
- Prepare small graphs for visualization or algorithmic analysis: `extract_subgraph`

### 4. Graph Partitioning & Structure Compression

- Partition nodes by nearest center: `voronoi_cells`
- Compress graph by communities, groups, or equivalence: `quotient_graph`

### 5. Graph Comparison, Reasoning & Merging

- Compare structural differences: `graph_edit_distance`
- Test conditional independence: `is_d_separator`
- Merge two graphs: `full_join`

### 6. Scenario‑based Selection Recommendations

- **View node attributes** → `get_node_attributes`
- **Find nodes within k hops of a node** → `k_hop_neighbors`
- **Find common friends / common trading objects of two nodes** → `common_neighbors`
- **Extract a local subgraph for visualization** → `extract_subgraph`
- **Find how two nodes are connected** → `get_paths_between_two_nodes`
- **Partition the graph by multiple centers** → `voronoi_cells`
- **Compare structural differences between two graphs** → `graph_edit_distance`
- **Compress communities into a higher‑level graph** → `quotient_graph`
- **Test conditional independence in causal / probabilistic graphs** → `is_d_separator`
- **Merge two graphs for joint analysis** → `full_join`

---

## VI. Engineering and Usage Considerations

1. **Control scale in k‑hop and path queries**  
   `k_hop_neighbors` and `get_paths_between_two_nodes` can explode quickly in high‑average‑degree graphs. Limit `k`, path length, and result counts.

2. **Clarify direction semantics for common neighbors**  
   In directed graphs, “common neighbors” may refer to common out‑neighbors, common in‑neighbors, or mixed. Clearly define the direction according to business logic.

3. **Limit scope before subgraph extraction**  
   When using `extract_subgraph`, first narrow down the set of nodes, edges, k‑hop range, or business tags.

4. **Graph edit distance is suitable for small or pattern graphs**  
   `graph_edit_distance` is computationally expensive; prefer small graphs, templates, or candidate structures.

5. **Quotient graph quality depends on grouping**  
   The result of `quotient_graph` is only as good as the node partition. Define community partitions, business groupings, or equivalence relations clearly.

6. **d‑separation is meaningful in directed probabilistic / causal graph contexts**  
   The interpretation of `is_d_separator` relies on graphical model semantics. Do not apply it to ordinary directed relationship graphs for causal conclusions.

7. **Check for node name conflicts before merging graphs**  
   When using `full_join`, ensure that the node namespaces of the two graphs are compatible to avoid mistakenly identifying distinct entities as the same node.

---

## VII. Typical Questions That Can Be Directly Answered

- “What attributes does this node have?”
- “Which nodes are within 2 hops of a given node?”
- “What common trading objects do two accounts have?”
- “Do two users have common friends?”
- “What paths exist from A to B?”
- “Extract a local subgraph around a node.”
- “Partition the whole graph into coverage regions around these center nodes.”
- “How structurally different are two flowcharts?”
- “Compress communities into a community‑level relationship graph.”
- “In this causal graph, does Z d‑separate X from Y?”
- “Merge two graphs into a joint graph for analysis.”
- “Are these two graphs only locally different, or globally very different?”

---

## VIII. Operator List

| No. | Operator Name                    | Description                                       |
|-----|----------------------------------|---------------------------------------------------|
| 1   | `get_node_attributes`            | Retrieve node attributes                         |
| 2   | `k_hop_neighbors`                | Query k‑hop neighbors                            |
| 3   | `common_neighbors`               | Query common neighbors                           |
| 4   | `extract_subgraph`               | Extract subgraph                                 |
| 5   | `get_paths_between_two_nodes`    | Query paths between two nodes                    |
| 6   | `voronoi_cells`                  | Graph Voronoi region partitioning                |
| 7   | `graph_edit_distance`            | Compute graph edit distance                      |
| 8   | `quotient_graph`                 | Construct quotient graph                         |
| 9   | `is_d_separator`                 | Test d‑separation condition                      |
| 10  | `full_join`                      | Graph full join merge                            |