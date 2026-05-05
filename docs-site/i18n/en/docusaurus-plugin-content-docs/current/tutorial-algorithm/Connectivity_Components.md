---
sidebar_position: 4
title: Connectivity & Components Operator Set
---

# Connectivity & Components Operator Set

**Operator Category**: Connectivity & Components (connectivity analysis, components, cuts/partitioning)

**Number of Algorithms**: 17

**Applicable Stages**: Network health check, island/partition identification, robustness evaluation, critical node/edge localization, fault/attack surface analysis, strong/weak connectivity analysis, bridge structure identification, SCC compression modeling

**Product Positioning**: Provides a unified capability foundation for “is the network connected / how many blocks / which nodes or edges cause splitting upon removal / minimum cuts to disconnect / how to compress directed graphs into component-level DAGs / which edges are local bridges”.

---

## I. Operator Set Overview

The Connectivity & Components operator set focuses on **connectivity analysis for undirected and directed graphs**, covering the following core questions:

1. **Connectivity and Connected Components**
   - Is the undirected graph fully connected?
   - How many connected components does the graph have?
   - Which nodes belong to each connected component?
   - Typical algorithms: `is_connected`, `connected_components`, `number_connected_components`

2. **Strong and Weak Connectivity Analysis for Directed Graphs**
   - Is the directed graph strongly connected?
   - Is it weakly connected when ignoring direction?
   - Which nodes form strongly or weakly connected components?
   - Typical algorithms: `is_strongly_connected`, `strongly_connected_components`, `is_weakly_connected`, `weakly_connected_components`

3. **Network Robustness Metrics**
   - What is the minimum number of nodes whose removal disconnects the graph?
   - What is the minimum number of edges whose removal disconnects the graph?
   - What is the minimum cut between two given nodes?
   - Typical algorithms: `node_connectivity`, `edge_connectivity`

4. **Minimum Cuts and Critical Structures**
   - Which set of nodes, when removed together, disconnects the graph?
   - Which set of edges, when removed together, disconnects the graph?
   - Which nodes are articulation points?
   - Which edges are bridges?
   - Typical algorithms: `minimum_node_cut`, `minimum_edge_cut`, `articulation_points`, `bridges`

5. **Bridge Structures and Biconnected Decomposition**
   - Which regions are internally more robust?
   - Which edges connect originally separate structural blocks?
   - Which edges cause component splitting when removed?
   - Typical algorithms: `bridge_components`, `biconnected_component_edges`, `local_bridges`

6. **Directed Component Compression**
   - How to compress strongly connected components into a component-level DAG?
   - How to extract high-level dependency structures from complex directed graphs?
   - Typical algorithms: `condensation`

---

## II. Operator Capability Classification

| Capability Type | Corresponding Operator | Description |
|---|---|---|
| Overall connectivity (undirected) | `is_connected` | Check if the undirected graph is a single connected component |
| Undirected connected components | `connected_components` | Output node sets of each connected component |
| Number of undirected components | `number_connected_components` | Return the number of connected components |
| Directed strong connectivity | `is_strongly_connected` | Check if every pair of nodes is mutually reachable in a directed graph |
| Directed strongly connected components | `strongly_connected_components` | Output SCC partitioning |
| Directed weak connectivity | `is_weakly_connected` | Check connectivity after ignoring direction |
| Directed weakly connected components | `weakly_connected_components` | Output WCC partitioning |
| Robustness metric (node) | `node_connectivity` | Minimum number of nodes whose removal disconnects the graph or a given s-t pair |
| Robustness metric (edge) | `edge_connectivity` | Minimum number of edges whose removal disconnects the graph or a given s-t pair |
| Minimum node cut set | `minimum_node_cut` | Smallest node set whose removal disconnects the graph or s-t |
| Minimum edge cut set | `minimum_edge_cut` | Smallest edge set whose removal disconnects the graph or s-t |
| Critical nodes (articulation points) | `articulation_points` | Nodes whose removal increases the number of connected components |
| Bridge-connected components | `bridge_components` | 2-edge-connected components based on bridges |
| Biconnected component edges | `biconnected_component_edges` | Output edge sets of biconnected components in an undirected graph |
| SCC condensation | `condensation` | Compress SCCs into a DAG |
| Bridge edges | `bridges` | Edges whose removal increases the number of connected components |
| Local bridges | `local_bridges` | Edges whose endpoints share no common neighbors; can compute span |

---

## III. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph
  - Undirected connectivity algorithms typically use `Graph`
  - Strong/weak connectivity and SCC condensation typically use `DiGraph`
  - Some cut and connectivity algorithms support both directed and undirected graphs

- **Common Output**
  - Boolean: `bool`
  - Component generators: `generator[set(node)]`
  - Connectivity metrics: `int`
  - Minimum cuts: `set(node)` or `set(edge)`
  - Articulation points: node iterator
  - Bridges: edge iterator
  - Biconnected components: `generator[list[edge]]`
  - Condensation graph: `DiGraph`

> Notes:
> - “Strongly connected / weakly connected” are meaningful only for **directed graphs**.
> - “Articulation points / bridges / biconnected components / bridge components” are typically used for **undirected** graph robustness analysis.
> - “Minimum cuts / node connectivity / edge connectivity” can be used for whole graphs or for specific node pairs `(s, t)` for local robustness.

---

## IV. Detailed Operator Descriptions

### 1. is_connected – Check if undirected graph is connected

**Description**  
Determines whether every pair of nodes in the undirected graph has a path connecting them.

**Product Value**
- First health check for a network
- Quickly identify islands, disconnected regions, or isolated nodes
- Useful as a pre-check for subsequent global algorithms

**Typical Scenarios**
- Check if a road network is fully connected
- Verify device interconnectivity forms a single network
- Detect fully isolated circles in social networks
- Identify disconnected subnets in supply chains

**Applicability & Characteristics**
- Graph type: Undirected
- Output: `bool`
- Complexity: `O(V + E)`

---

### 2. connected_components – Undirected connected components

**Description**  
Outputs the node set of each connected component in the undirected graph. Nodes in the same component are mutually reachable; different components are disconnected.

**Product Value**
- Identify regions, communities, or subsystems that are isolated from one another
- Provide boundaries for partitioned statistics, modeling, or scheduling
- Help locate isolated components and abnormal fragmentation

**Typical Scenarios**
- Logistics site network: separate operational regions
- Device network: find isolated subnets
- Social network: identify non-interacting circles
- Corporate relationship network: identify unrelated corporate clusters

**Applicability & Characteristics**
- Graph type: Undirected
- Output: `generator[set(node)]`
- Complexity: `O(V + E)`

---

### 3. number_connected_components – Number of connected components

**Description**  
Returns the number of connected components in the undirected graph.

**Product Value**
- Quickly quantify the degree of network fragmentation
- Can be used as a network health KPI
- Monitor how many pieces a network splits into before/after a failure

**Typical Scenarios**
- Evaluate number of partitioned areas after road network disruption
- Count subnets after device network failure
- Count isolated teams in a collaboration network
- Estimate number of independent fraud groups in a risk network

**Applicability & Characteristics**
- Graph type: Undirected
- Output: `int`
- Complexity: `O(V + E)`

---

### 4. is_strongly_connected – Check if directed graph is strongly connected

**Description**  
Determines whether the directed graph is strongly connected, i.e., for every pair of nodes `u` and `v`, there exists a path `u → v` and a path `v → u`.

**Product Value**
- Evaluate whether a directed network forms complete mutual reachability
- Suitable for analyzing systems like service calls, page navigation, or fund circulation
- Identify directional blocking

**Typical Scenarios**
- Service invocation: whether every service can reach every other via call chains
- Page navigation: whether every page can be reached and returned from
- Transaction network: whether closed‑loop funds can circulate
- State machine: whether all states are mutually reachable

**Applicability & Characteristics**
- Graph type: Directed
- Output: `bool`
- Complexity: `O(V + E)`

---

### 5. strongly_connected_components – Strongly connected components (SCCs)

**Description**  
Outputs the strongly connected components of a directed graph. Within each SCC, every node is mutually reachable.

**Product Value**
- Identify cyclic modules, closed‑loop groups, and interdependent structures
- Useful for dependency analysis, deadlock detection, and modular decomposition
- Foundational for directed graph compression and hierarchical modeling

**Typical Scenarios**
- Service dependency graph: find circular dependencies
- Flowcharts: identify loops that can return to start
- Transaction networks: identify fund‑circulation cliques
- Code dependencies: locate circular imports or references

**Applicability & Characteristics**
- Graph type: Directed
- Output: `generator[set(node)]`
- Complexity: `O(V + E)`

---

### 6. is_weakly_connected – Check if directed graph is weakly connected

**Description**  
Treats the directed graph as undirected (ignores direction) and checks if it is connected.

**Product Value**
- Determine if the directed network is structurally one piece
- Even if not mutually reachable, check overall connectivity
- Suitable for macroscopic connectivity checks of directed graphs

**Typical Scenarios**
- Email network: check if the organization is fully connected ignoring direction
- Follow network: see if users are isolated into separate groups
- Page links: check if sites are partitioned into isolated islands
- Service calls: determine if business domains are structurally related

**Applicability & Characteristics**
- Graph type: Directed
- Output: `bool`
- Complexity: `O(V + E)`

---

### 7. weakly_connected_components – Weakly connected components (WCCs)

**Description**  
After ignoring direction, partitions the directed graph into weakly connected components.

**Product Value**
- Identify structural partitions of a directional network
- Provide boundaries for further analysis (SCC, centrality, community detection)
- Identify isolated business domains or propagation domains

**Typical Scenarios**
- Follow network: identify non‑interacting user domains
- Email network: identify groups that do not communicate with each other
- Service calls: identify unrelated business systems
- Transaction network: identify account groups with no transaction connections

**Applicability & Characteristics**
- Graph type: Directed
- Output: `generator[set(node)]`
- Complexity: `O(V + E)`

---

### 8. node_connectivity – Node connectivity

**Description**  
Returns the minimum number of nodes that must be removed to disconnect the graph. If `s, t` are specified, returns the minimum number of nodes whose removal disconnects `s` from `t`.

**Product Value**
- Quantify the network’s ability to withstand node failures or attacks
- Assess redundancy of critical equipment, roles, or accounts
- Support robustness scoring and reinforcement budgeting

**Key Parameters**
- `s, t`: source and target for local connectivity analysis
- `flow_func`: max‑flow implementation, affects performance

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `int`
- Complexity: Depends on max‑flow algorithm

---

### 9. edge_connectivity – Edge connectivity

**Description**  
Returns the minimum number of edges that must be removed to disconnect the graph. If `s, t` are specified, returns the minimum number of edges whose removal disconnects `s` from `t`.

**Product Value**
- Quantify the network’s ability to withstand link failures or edge attacks
- Plan link redundancy, network hardening, and disaster recovery
- Identify low‑redundancy connection bottlenecks

**Key Parameters**
- `s, t`: source and target
- `flow_func`: max‑flow implementation
- `cutoff`: early stop threshold, useful to check if connectivity is below a certain level

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `int`
- Complexity: Depends on max‑flow algorithm

---

### 10. minimum_node_cut – Minimum node cut set

**Description**  
Returns a set of nodes of minimum size whose removal disconnects the graph. If `s, t` are specified, returns a set of nodes whose removal disconnects `s` from `t`.

**Product Value**
- Directly identify the most vulnerable node sets
- Support failure simulation, attack surface assessment, and prioritization of reinforcement
- More actionable than plain connectivity metrics

**Key Parameters**
- `s, t`: source and target for local cut analysis
- `flow_func`: max‑flow implementation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `set(node)`
- Complexity: Depends on max‑flow algorithm

---

### 11. minimum_edge_cut – Minimum edge cut set

**Description**  
Returns a set of edges of minimum size whose removal disconnects the graph. If `s, t` are specified, returns a set of edges whose removal disconnects `s` from `t`.

**Product Value**
- Directly locate the most vulnerable link sets
- Plan backup routes, link hardening, and failure simulation
- Assess vulnerability of cross‑regional or cross‑system connections

**Key Parameters**
- `s, t`: source and target
- `flow_func`: max‑flow implementation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `set(edge)`
- Complexity: Depends on max‑flow algorithm

---

### 12. articulation_points – Articulation points

**Description**  
In an undirected graph, a node whose removal increases the number of connected components is an articulation point (cut vertex).

**Product Value**
- Identify typical single points of failure
- Complement node connectivity and minimum node cut analyses
- Quickly locate structurally weak points

**Typical Scenarios**
- Road network: which intersections, if closed, would split the traffic network
- Data center: which devices, if failed, would split the network
- Social network: which users, if leaving, would split communities
- Supply chain: which companies, if failing, would break upstream/downstream

**Applicability & Characteristics**
- Graph type: Undirected
- Output: node iterator
- Complexity: `O(V + E)`

---

### 13. bridge_components – Bridge‑connected components

**Description**  
Finds all 2‑edge‑connected components (bridge components) in an undirected graph. Inside a bridge component, no single edge failure disconnects the component; components are connected by bridges.

**Product Value**
- Decompose the network into blocks with internal edge redundancy
- Support network partitioning, resilience module identification, and layered hardening
- Help locate backbone structures and vulnerable connections

**Typical Scenarios**
- Road network: regions with alternative routes
- Data center: device groups with higher link redundancy
- Collaboration network: stable working groups with strong internal ties
- Communication network: sub‑nets resistant to single link failure

**Applicability & Characteristics**
- Graph type: Undirected
- Output: `generator[set(node)]`
- Complexity: `O(V + E)`

---

### 14. biconnected_component_edges – Biconnected component edges

**Description**  
Outputs the edge sets of biconnected components in an undirected graph. A biconnected component typically has no articulation point that can split it.

**Product Value**
- Identify structural blocks with internal node redundancy
- Analyze which areas are less likely to be split by a single node failure
- Combine with `articulation_points` for block‑cutpoint structure analysis

**Typical Scenarios**
- Road network: areas resistant to single intersection closure
- Communication network: link blocks resistant to single device failure
- Social network: groups with highly sturdy relationships
- Supply chain: subsystems with strong node redundancy

**Applicability & Characteristics**
- Graph type: Undirected
- Output: `generator[list[edge]]`
- Complexity: Typically `O(V + E)`

---

### 15. condensation – SCC condensation graph

**Description**  
Compresses each strongly connected component of a directed graph into a single node, producing a new directed acyclic graph (DAG). Edges in the condensation represent dependencies or reachability between SCCs.

**Product Value**
- Abstract complex directed graphs into component‑level structures
- Extract high‑level DAGs from cyclic dependencies
- Support modular analysis, dependency layering, and process simplification

**Typical Scenarios**
- Service dependencies: compress mutually dependent services into modules
- Code dependencies: compress circular‑reference packages to analyze package‑level hierarchies
- Transaction networks: compress fund‑circulation cliques to observe flows between cliques
- Process networks: compress loop steps and then topologically sort high‑level steps

**Applicability & Characteristics**
- Graph type: Directed
- Output: `DiGraph` (a DAG)
- Complexity: Typically `O(V + E)`

---

### 16. bridges – Bridge edges

**Description**  
Finds all bridges (cut edges) in an undirected graph. Removing a bridge increases the number of connected components.

**Product Value**
- Directly identify single link failure risks
- Support link hardening, backup planning, and network vulnerability analysis
- Complement `articulation_points` for a “critical edge + critical node” diagnostic combination

**Typical Scenarios**
- Road network: roads whose closure would split regions
- Communication network: links whose failure would isolate subnets
- Logistics network: transport lines whose interruption would break supply
- Device network: critical connections without backup paths

**Applicability & Characteristics**
- Graph type: Undirected
- Output: edge iterator
- Complexity: `O(V + E)`

---

### 17. local_bridges – Local bridges

**Description**  
Finds local bridges. A local bridge is an edge whose endpoints have no common neighbors; removing it lengthens the shortest alternative path between its endpoints. Optionally computes the span of the local bridge.

**Product Value**
- Identify cross‑circle connections in local structures
- Discover edges that may not be global bridges but are critical in local neighborhoods
- Suitable for social networks, recommendation networks, and local vulnerability analysis

**Typical Scenarios**
- Social network: weak ties connecting different friend circles
- Collaboration network: cross‑team collaboration edges
- Recommendation network: edges bridging interest groups
- Risk network: suspicious relations connecting local fraud clusters

**Key Parameters**
- `with_span`: whether to return the span of local bridges
- `weight`: edge weight field used to compute alternative path length

**Applicability & Characteristics**
- Graph type: Undirected
- Output: edge iterator or edge‑span pairs
- Suitable for local bridge and weak tie analysis

---

## V. Recommended Usage Guide (Practical Advice)

- **First answer: “Is it one connected piece?”**
  - Undirected: `is_connected`
  - Directed structural connectivity: `is_weakly_connected`
  - Directed mutual reachability: `is_strongly_connected`

- **Then: “How many pieces and what are they?”**
  - Undirected: `connected_components` + `number_connected_components`
  - Directed: `weakly_connected_components` / `strongly_connected_components`

- **Then: “Robustness and breakpoints”**
  - Node redundancy metric: `node_connectivity`
  - Edge redundancy metric: `edge_connectivity`
  - Minimum node cut: `minimum_node_cut`
  - Minimum edge cut: `minimum_edge_cut`

- **Find intuitive critical points and edges**
  - Articulation points: `articulation_points`
  - Bridges: `bridges`
  - Local bridges: `local_bridges`

- **Component‑level structural analysis**
  - Bridge components: `bridge_components`
  - Biconnected component edges: `biconnected_component_edges`
  - SCC condensation: `condensation`

---

## VI. Typical Questions That Can Be Directly Answered (Examples)

- “Is this undirected graph connected? If not, how many components?”
- “Is the directed graph weakly connected? Is it strongly connected?”
- “Output all strongly connected components and sort them by size.”
- “Compress SCCs into a DAG and show the dependency levels between components.”
- “What is the minimum number of edges or nodes whose removal would disconnect this network?”
- “Give me a minimum node cut set or a minimum edge cut set.”
- “List all articulation points for single point of failure analysis.”
- “List all bridges, i.e., critical links without backup paths.”
- “Which edges are local bridges connecting different local circles?”
- “Decompose the network into bridge components and see which regions are internally robust.”
- “Output biconnected component edges to analyze resistance to single node failures.”
- “Show me all simple cycles with length ≤ 6.”
- “Output the cycle basis of this undirected graph.”
- “Compute the triadic census for this directed graph to see transitive vs cyclic triples.”
- “Does this community partition cover all nodes with no overlaps? Check `is_partition`.”
- “Which community partition has higher modularity?”

---

## VII. Engineering and Usage Considerations

1. **Differentiate graph types first**
   - Undirected graphs: `is_connected`, `connected_components`, `bridges`, `articulation_points`, etc.
   - Directed graphs: `is_strongly_connected`, `strongly_connected_components`, `is_weakly_connected`, `weakly_connected_components`, `condensation`, etc.

2. **Strong vs. weak connectivity have different meanings**
   - Strong connectivity requires mutual reachability in the directed sense.
   - Weak connectivity only requires structural connectivity ignoring direction.
   - In many directed business networks, the two can differ greatly.

3. **Global connectivity vs. local s‑t connectivity**
   - Global `node_connectivity` / `edge_connectivity` measure the most vulnerable part of the entire graph.
   - Specifying `s, t` measures redundancy between two particular nodes.

4. **Articulation points and bridges are good for quick health checks**
   - `articulation_points` and `bridges` are usually fast to compute and intuitive to interpret.
   - They quickly identify obvious single‑point‑of‑failure risks.

5. **Minimum cuts are more suitable for reinforcement planning**
   - `minimum_node_cut` and `minimum_edge_cut` directly give sets that need to be protected or hardened.
   - Useful for failure simulation, attack simulation, and disaster recovery planning.

6. **SCC condensation helps reduce complexity of directed graphs**
   - When the original graph contains many cycles, first apply `condensation`.
   - The resulting graph is a DAG, suitable for topological sorting, hierarchical analysis, and high‑level visualization.

7. **Local bridges are not necessarily global bridges**
   - `bridges` increase the number of connected components in the whole graph.
   - `local_bridges` emphasize bridging roles in local neighborhoods, better suited for weak ties and cross‑circle connections in social networks.

---

## VIII. Operator List

| No. | Operator Name | Description |
|---:|---|---|
| 1 | `is_connected` | Check if undirected graph is connected |
| 2 | `connected_components` | Undirected connected components |
| 3 | `number_connected_components` | Number of undirected connected components |
| 4 | `is_strongly_connected` | Check if directed graph is strongly connected |
| 5 | `strongly_connected_components` | Strongly connected components |
| 6 | `is_weakly_connected` | Check if directed graph is weakly connected |
| 7 | `weakly_connected_components` | Weakly connected components |
| 8 | `node_connectivity` | Node connectivity |
| 9 | `edge_connectivity` | Edge connectivity |
| 10 | `minimum_node_cut` | Minimum node cut |
| 11 | `minimum_edge_cut` | Minimum edge cut |
| 12 | `articulation_points` | Articulation points (cut vertices) |
| 13 | `bridge_components` | Bridge‑connected components |
| 14 | `biconnected_component_edges` | Biconnected component edges |
| 15 | `condensation` | SCC condensation graph |
| 16 | `bridges` | Bridge edges (cut edges) |
| 17 | `local_bridges` | Local bridges |