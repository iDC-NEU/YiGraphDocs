---
sidebar_position: 1
---

# Basics Operator Set

**Operator Category**: Basics (Graph Structure Validation, Basic Traversal and Structural Legality Verification)

**Number of Algorithms**: 15

**Applicable Stages**: Graph legality verification, structure diagnosis, dependency analysis, hierarchical/upstream-downstream analysis, traversal and sorting, graph structure matching, planarity analysis, special graph type identification

**Product Positioning**: Provides basic operator capability foundation for "Is this graph legal / Does it have cycles / Can it be sorted / Who are the upstream/downstream / How to traverse / Are two graph structures consistent / Does it satisfy special graph structure" questions.

---

## 1. Operator Set Overview

The Basics operator set focuses on **basic structural property judgment of graphs**, **basic traversal/sorting capabilities**, and **special graph structure identification**, mainly answering the following questions:

1. **Structural Legality**
   - Is it a tree / forest / DAG?
   - Does it contain cycles?
   - Does it satisfy planar graph, tournament graph and other special structures?

2. **Dependency and Hierarchical Relationships**
   - Who are the ancestors of a node? (ancestors)
   - Who are the descendants of a node? (descendants)
   - Can it be sorted according to dependency relationships?

3. **Traversal and Structure Generation**
   - How to expand layer by layer from a node? (BFS)
   - How to deeply explore a relationship chain? (DFS)

4. **Dependency Sorting Problems**
   - Can it be topologically sorted?
   - What are the feasible execution orders?
   - How to stably sort among multiple feasible orders?

5. **Structure Similarity and Special Graph Identification**
   - Are two graphs structurally equivalent? (is_isomorphic)
   - Is the graph AT-free?
   - Can the graph be planar embedded? (check_planarity)
   - Is the directed graph a tournament? (is_tournament)
   - Can a given score sequence construct a tournament? (score_sequence)

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operator | Function Description |
|---|---|---|
| Tree/Forest Determination | `is_tree`, `is_forest` | Determine if the graph satisfies tree or forest structure |
| DAG Determination | `is_directed_acyclic_graph` | Determine if the directed graph is acyclic |
| Basic Traversal (Breadth) | `bfs_tree` | Build BFS layered traversal tree from starting point |
| Basic Traversal (Depth) | `dfs_tree` | Build DFS depth traversal tree from starting point |
| Topological Sort | `topological_sort` | Output one legal topological order |
| Stable Topological Sort | `lexicographical_topological_sort` | Output topological order constrained by lexicographical order |
| Topological Full Enumeration | `all_topological_sorts` | Enumerate all legal topological orders in DAG |
| Upstream Analysis | `ancestors` | Query all ancestor nodes of specified node |
| Downstream Analysis | `descendants` | Query all successor nodes of specified node |
| Graph Isomorphism | `is_isomorphic` | Determine if two graphs have the same structure |
| AT-free Graph Determination | `is_at_free` | Determine if graph is AT-free |
| Planarity Detection | `check_planarity` | Determine if graph can be drawn on plane without edge crossings |
| Tournament Determination | `is_tournament` | Determine if directed graph is a tournament |
| Tournament Score Sequence | `score_sequence` | Calculate score sequence of nodes in tournament |

---

## 3. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph
- **Common Output Types**:
  - Determination: `bool`
  - Set: `set(node)`
  - Traversal: `NetworkX DiGraph` (spanning tree)
  - Sorting: `list` / `iterator`
  - Structure: Graph embedding object, sequence or matching result

---

## 4. Detailed Operator Descriptions

### 1. is_tree —— Tree Structure Determination

**Function Description**  
Determine if an undirected graph satisfies the tree definition of "connected + acyclic".

**Product Value**
- Quickly verify if hierarchical structure is legal
- Prevent implicit cycles or structural breaks
- Suitable as pre-verification for tree data modeling

**Typical Scenarios**
- Organizational structure verification
- Task dependency tree verification
- Material/assembly hierarchy verification
- Family relationship or classification system verification

**Applicability and Characteristics**
- Graph Type: Undirected graph
- Complexity: `O(V + E)`

---

### 2. is_forest —— Forest Structure Determination

**Function Description**  
Determine if an undirected graph consists of multiple disconnected trees.

**Product Value**
- Verify if multiple independent hierarchical structures are legal
- Check for cross-component cycles
- Suitable for multi-subsystem, multi-organization, multi-chain structure analysis

**Typical Scenarios**
- Multi-department organizational structure
- Multi-product line assembly relationships
- Multiple independent fund chain analysis
- Multiple classification tree legality verification

**Applicability and Characteristics**
- Graph Type: Undirected graph
- Complexity: `O(V + E)`

---

### 3. is_directed_acyclic_graph —— DAG Determination

**Function Description**  
Determine if a directed graph is a Directed Acyclic Graph (DAG).

**Product Value**
- Pre-verification for topological sort, critical path and other algorithms
- Identify circular dependency risks
- Determine if processes, tasks, and reference relationships can be linearly expanded

**Typical Scenarios**
- Project task dependencies
- Compilation/build dependencies
- Data lineage analysis
- Approval and workflow systems
- Call chain and reference chain analysis

**Applicability and Characteristics**
- Graph Type: Directed graph
- Complexity: `O(V + E)`

---

### 4. bfs_tree —— Breadth-First Search Tree

**Function Description**  
Build a BFS traversal tree in hierarchical order from the specified node.

**Product Value**
- Naturally forms "how many hops from starting point" hierarchical structure
- Suitable for displaying diffusion scope and hierarchical relationships
- Strong interpretability for shortest hop relationships

**Typical Scenarios**
- Social circle diffusion
- Organizational hierarchy display
- City/station layered reachability analysis
- Risk propagation hierarchical impact range analysis

**Key Parameters**
- `source`: Starting node
- `depth_limit`: Maximum traversal depth

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Complexity: `O(V + E)`

---

### 5. dfs_tree —— Depth-First Search Tree

**Function Description**  
Build a DFS traversal tree by going as deep as possible along a path from the specified node.

**Product Value**
- Suitable for path mining and chain relationship analysis
- Convenient for discovering deep dependencies, deep relationships and long chain structures
- Commonly used for search, backtracking and structure exploration

**Typical Scenarios**
- Call chain analysis
- File/directory scanning
- Deep investigation of relationship chains
- Deep dependency tracking
- Graph structure exploration

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Complexity: `O(V + E)`

---

### 6. topological_sort —— Topological Sort

**Function Description**  
Generate a linear order that satisfies dependency constraints in a DAG.

**Product Value**
- Provide one solution for "executable order"
- Basic capability for scheduling, building, and process orchestration
- Convert complex dependency graphs into linear execution sequences

**Typical Scenarios**
- Project task ordering
- Build systems
- Approval processes
- Data processing pipelines
- Course prerequisite ordering

**Applicability and Characteristics**
- Graph Type: Directed Acyclic Graph (DAG)
- Complexity: `O(V + E)`

---

### 7. lexicographical_topological_sort —— Lexicographical Topological Sort

**Function Description**  
Among all feasible topological orders, output the stable topological order according to specified lexicographical rules.

**Product Value**
- Output results are stable and reproducible
- Suitable for productized display and automated processes
- Avoid different sorting results from multiple calculations of the same graph

**Typical Scenarios**
- Compilation/build order
- Page generation order
- Approval number sorting
- Multi-task scheduling stable sorting
- Test case execution order generation

**Applicability and Characteristics**
- Graph Type: Directed Acyclic Graph (DAG)
- Complexity: `O(E + V log V)`

---

### 8. all_topological_sorts —— Full Topological Order Enumeration

**Function Description**  
Enumerate all possible legal topological sorting results in a DAG.

**Product Value**
- Explore all feasible execution plans
- Used for solution enumeration, decision analysis and path comparison
- Help identify scheduling flexibility under dependency constraints

**Typical Scenarios**
- Project scheduling multi-solution analysis
- Multiple approval path exploration
- Multiple scheduling solution comparison
- Task execution order optimization
- Small-scale dependency graph exhaustive analysis

**Notes**
- Result count may grow exponentially
- More suitable for small-scale DAGs
- For large graphs, recommend using single topological sort or stable topological sort

---

### 9. ancestors —— Upstream Node Query

**Function Description**  
Return the set of all upstream nodes that can reach the specified node.

**Product Value**
- Quickly locate "who influenced me"
- Suitable for traceability, responsibility chain, and reference source analysis
- Can be used for dependency risk and impact source identification

**Typical Scenarios**
- Fund source tracing
- Data lineage upstream analysis
- Reference chain analysis
- Management reporting chain query
- Task dependency source analysis

**Applicability and Characteristics**
- Graph Type: Directed graph
- Output: `set(node)`
- Complexity: `O(V + E)`

---

### 10. descendants —— Downstream Node Query

**Function Description**  
Return the set of all downstream nodes reachable from the specified node.

**Product Value**
- Measure the impact scope of a node
- Support propagation analysis, dependency impact assessment and risk diffusion analysis
- Quickly answer "who will I influence"

**Typical Scenarios**
- Public opinion/information diffusion
- Task delay impact assessment
- Fund flow analysis
- Service call impact analysis
- Data lineage downstream analysis

**Applicability and Characteristics**
- Graph Type: Directed graph
- Output: `set(node)`
- Complexity: `O(V + E)`

---

### 11. is_isomorphic —— Graph Isomorphism Determination

**Function Description**  
Determine if two graphs are structurally equivalent, i.e., whether they can be made identical through node renaming.

**Product Value**
- Identify structurally identical graphs with different node names
- Support template matching, pattern recognition and duplicate structure discovery
- Can be used for graph structure deduplication and similar structure merging

**Typical Scenarios**
- Process template matching
- Network structure deduplication
- Fraud gang structure identification
- Molecular structure comparison
- Subsystem topology structure consistency verification

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: `bool`
- Note: Graph isomorphism has high computational cost; use cautiously for large graphs

---

### 12. is_at_free —— AT-free Graph Determination

**Function Description**  
Determine if a graph is AT-free, i.e., whether the graph does not contain asteroidal triple (three points satisfying specific path avoidance relationships).

**Product Value**
- Used to identify graphs with special structural properties
- Support graph theory structure analysis and special graph class modeling
- Can serve as pre-judgment for some advanced graph algorithms or graph decomposition tasks

**Typical Scenarios**
- Special graph class identification
- Graph structure theory analysis
- Interval graph, chordal graph and related structure research
- Network structure constraint verification

**Applicability and Characteristics**
- Graph Type: Usually used for undirected graphs
- Output: `bool`
- Suitable for structure diagnosis, special graph identification and algorithm pre-filtering

---

### 13. check_planarity —— Planarity Detection

**Function Description**  
Determine if a graph is planar, i.e., whether the graph can be drawn on a plane without edge crossings.

**Product Value**
- Determine if network is suitable for planar layout
- Support topological visualization, route planning and structure simplification
- Can be used to discover complex crossing relationships and layout bottlenecks

**Typical Scenarios**
- Network topology visualization
- Circuit/pipeline structure verification
- Road network and spatial network analysis
- Graph layout optimization
- Planar embedding analysis

**Common Output**
- Is planar: `bool`
- Planar embedding structure: planar embedding

**Applicability and Characteristics**
- Graph Type: Usually used for undirected graphs
- Complexity: Usually linear level `O(V + E)`

---

### 14. is_tournament —— Tournament Determination

**Function Description**  
Determine if a directed graph is a tournament. A tournament requires that any two different nodes have exactly one directed edge between them.

**Product Value**
- Suitable for pairwise comparison, win-loss relationships, preference relationships
- Can determine if pairwise competitive relationships are complete
- Support ranking, ranking and competitive structure analysis

**Typical Scenarios**
- Competition win-loss relationship analysis
- Pairwise preference comparison
- Ranking system verification
- Decision preference graph analysis
- Pairwise PK relationship modeling

**Applicability and Characteristics**
- Graph Type: Directed graph
- Output: `bool`
- Requires complete pairwise relationships between nodes with unique direction

---

### 15. score_sequence —— Tournament Score Sequence

**Function Description**  
Calculate the score sequence of each node in a tournament, usually corresponding to the out-degree sequence of nodes.

**Product Value**
- Used to characterize the distribution of win-loss ability of nodes in a tournament
- Support ranking, strength analysis and overall competitive landscape judgment
- Can be combined with tournament determination for pairwise comparison network analysis

**Typical Scenarios**
- Competition result statistics
- Ranking system analysis
- Preference relationship strength analysis
- Node competitiveness evaluation
- Tournament structure summary

**Applicability and Characteristics**
- Graph Type: Tournament / Directed graph
- Output: Score sequence
- Usually used together with `is_tournament`

---

## 5. Recommended Usage Guide

### 1. Graph Structure Legality Verification

- Determine if tree: `is_tree`
- Determine if forest: `is_forest`
- Determine if DAG: `is_directed_acyclic_graph`
- Determine if planar graph: `check_planarity`
- Determine if tournament: `is_tournament`
- Determine if AT-free graph: `is_at_free`

### 2. Dependency and Upstream-Downstream Analysis

- Query upstream nodes: `ancestors`
- Query downstream nodes: `descendants`
- Determine if sortable: `is_directed_acyclic_graph`
- Generate execution order: `topological_sort`

### 3. Traversal and Relationship Expansion

- Hierarchical diffusion analysis: `bfs_tree`
- Deep chain path analysis: `dfs_tree`

### 4. Sorting and Scheduling

- Provide one execution order: `topological_sort`
- Provide stable execution order: `lexicographical_topological_sort`
- Enumerate all execution orders: `all_topological_sorts`

### 5. Structure Matching and Special Graph Identification

- Determine if two graph structures are consistent: `is_isomorphic`
- Determine if graph can be drawn planar: `check_planarity`
- Determine if tournament: `is_tournament`
- Get tournament score sequence: `score_sequence`

---

## 6. Typical Answerable Questions

- "Is this dependency graph a DAG?"
- "Is this organizational structure a tree?"
- "Is this graph a forest structure?"
- "Which downstream tasks will be affected if a certain task is delayed?"
- "List all upstream sources of a certain node."
- "Starting from this node, what nodes are there at each level?"
- "Starting from this node, which nodes will be traversed along the relationship chain?"
- "Give a reasonable execution order."
- "Give me a stable execution order with smallest number priority."
- "How many legal execution plans are there in total?"
- "Are the structures of these two graphs the same?"
- "Can this network be drawn as a planar graph without edge crossings?"
- "Is this directed graph a tournament?"
- "What are the win-loss scores of each node in this tournament?"
- "Is this graph an AT-free graph?"

---

## 7. Operator List

| No. | Operator Name | Description |
|---|---|---|
| 1 | `is_tree` | Determine if tree structure |
| 2 | `is_forest` | Determine if forest structure |
| 3 | `is_directed_acyclic_graph` | DAG determination |
| 4 | `bfs_tree` | Breadth-first search tree |
| 5 | `dfs_tree` | Depth-first search tree |
| 6 | `topological_sort` | Topological sort |
| 7 | `lexicographical_topological_sort` | Lexicographical topological sort |
| 8 | `all_topological_sorts` | All topological sorts enumeration |
| 9 | `ancestors` | Upstream node query |
| 10 | `descendants` | Downstream node query |
| 11 | `is_isomorphic` | Graph isomorphism determination |
| 12 | `is_at_free` | AT-free graph determination |
| 13 | `check_planarity` | Planarity detection |
| 14 | `is_tournament` | Tournament determination |
| 15 | `score_sequence` | Tournament score sequence |
