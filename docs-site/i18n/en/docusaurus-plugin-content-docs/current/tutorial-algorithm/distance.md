---
sidebar_position: 14
title: Distance & Measures Operator Set
---

# Distance & Measures Operator Set

**Operator Category**: Distance & Measures

**Number of Algorithms**: 24

**Applicable Stages**: Network health check, topological span assessment, center/periphery identification, global compactness analysis, graph resistance and random‑walk analysis, propagation/tracing cost evaluation, assortativity analysis, connection preference analysis, mixing matrix modeling, structural preference diagnosis

**Product Positioning**: Provides a unified capability base for answering “how large is the network span / which nodes are in the center or on the periphery / what is the overall distance cost / whether the graph is compact / whether nodes tend to connect to similar nodes / whether high‑degree nodes connect to each other / how different attributes mix in connections / how neighbor‑degree structure is distributed”.

---

## I. Operator Set Overview

The Distance & Measures operator set focuses on **global distance structure, center‑periphery positioning, overall compactness, random‑walk stability, graph resistance characteristics, assortative mixing preferences, and mixing patterns**, addressing the following questions:

1. **Distance and Span Structure**
   - How far is a node from its farthest reachable node?
   - What is the distance between the two farthest nodes in the network?
   - Where is the optimal center of the network?
   - Which nodes lie on the structural periphery?
   - Is the network compact, and what is its overall distance cost?

2. **Center and Periphery Identification**
   - Which nodes are best suited as monitoring entries, service centers, or coordination hubs?
   - Which nodes are most remote or hardest to cover?
   - How are the radius, diameter, and center sets distributed?

3. **Graph Resistance and Random‑Walk Measures**
   - What is the resistance distance between two nodes?
   - What is the effective graph resistance of the whole graph?
   - How efficient is long‑term random‑walk access on the graph?
   - Does the network exhibit high diffusion or reachability cost?

4. **Assortativity and Connection Preferences**
   - Do high‑degree nodes tend to connect to high‑degree nodes?
   - Do nodes with the same attribute tend to connect to each other?
   - Are nodes with similar numeric attributes more likely to be connected?
   - Is the network assortative, disassortative, or neutral?

5. **Mixing Matrices and Edge‑Endpoint Relationships**
   - How frequently do different attribute classes connect to each other?
   - How do nodes of different degrees connect?
   - How are attribute values or degrees paired across each edge?
   - Can connection preferences be turned into matrices or dictionaries for downstream modeling?

---

## II. Operator Capability Classification

| Capability | Operator | Description |
| --- | --- | --- |
| Graph diameter | `diameter` | Compute maximum eccentricity |
| Graph radius | `radius` | Compute minimum eccentricity |
| Graph center | `center` | Return center nodes with minimum eccentricity |
| Graph periphery | `periphery` | Return peripheral nodes with eccentricity equal to diameter |
| Node farthest distance | `eccentricity` | Compute distance to farthest reachable node |
| Wiener index | `wiener_index` | Compute sum of all-pairs shortest path distances |
| Degree assortativity | `degree_assortativity_coefficient` | Measure correlation of degrees across edges |
| Attribute assortativity | `attribute_assortativity_coefficient` | Measure tendency of same-category nodes to connect |
| Efficiency | `efficiency` | Compute inverse of shortest path distance between node pairs |
| Average neighbor degree | `average_neighbor_degree` | Compute average degree of each node's neighbors |
| Average degree connectivity | `average_degree_connectivity` | Compute average neighbor degree by degree group |
| Communicability | `communicability` | Quantify communication strength considering all walks |
| Node redundancy | `node_redundancy` | Measure indirect interconnection of a node's neighbors in bipartite graphs |
| Closeness vitality | `closeness_vitality` | Evaluate node contribution to global compactness |
| Non-randomness | `non_randomness` | Quantify structural deviation from random graphs |
| Rich-club coefficient | `rich_club_coefficient` | Measure edge density among high-degree nodes |
| Reciprocity | `reciprocity` | Measure tendency toward mutual connections in directed graphs |
| Resistance distance | `resistance_distance` | Compute resistance distance between node pairs |
| Flow hierarchy | `flow_hierarchy` | Measure hierarchical degree of directed networks |
| Graph barycenter | `barycenter` | Return nodes minimizing total shortest-path distance |
| Estrada index | `estrada_index` | Scalar spectral measure of global compactness |
| Girth | `girth` | Return length of shortest cycle |
| Harmonic diameter | `harmonic_diameter` | Measure effective distance scale using harmonic mean |
| Kemeny constant | `kemeny_constant` | Measure average access characteristics of random walk to steady state |

---

## III. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph
- **Common Input Parameters**:
  - `weight`: Edge weight field name, function, or `None`
  - `e`: Pre‑computed eccentricity dictionary (reused by `radius`, `diameter`, `center`, `periphery`)
  - `sp`: Pre‑computed shortest‑path distance dictionary (for `eccentricity`, `barycenter`, `harmonic_diameter`)
  - `usebounds`: Acceleration for undirected graphs using bounds
  - `attribute`: Node attribute name (for attribute‑based assortativity and mixing)
  - `nodes`: Subset of nodes to restrict computation
  - `x` / `y`: Degree type for source/target in directed graphs (`in` / `out`)
  - `source` / `target`: Direction or degree type for neighbor‑degree algorithms
  - `normalized`: Whether to normalize mixing matrices or dictionaries
- **Common Output Types**:
  - Distance measures: numeric, list of nodes, or `dict[node → value]`
  - Resistance / random‑walk: numeric or node‑pair measures
  - Assortativity: float correlation coefficient
  - Neighbor degree: `dict[node → average_degree]` or `dict[degree → average_neighbor_degree]`
  - Mixing relations: matrix, nested dictionary, or edge‑endpoint value iterator

---

## IV. Detailed Operator Descriptions

### 1. diameter – Graph Diameter

**Description**  
The maximum eccentricity among all nodes. It is the longest shortest‑path distance between any two nodes.

**Product Value**
- Measures the maximum span of the network
- Indicates worst‑case propagation, tracking, or collaboration cost
- Allows structural comparison across networks

**Typical Scenarios**
- Social network span analysis
- Maximum detour distance in transportation
- Maximum hierarchical distance in supply chains
- Upper bound of communication latency
- Topology health check

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: numeric
- Interpret carefully for disconnected graphs

---



---

### 2. radius – Graph Radius

**Description**  
The minimum eccentricity among all nodes. It is the smallest possible worst‑case distance from an optimal center.

**Product Value**
- Measures the best‑center worst‑case coverage distance
- Indicates global compactness
- Useful for hub location and coordination ability assessment

**Typical Scenarios**
- Service center placement
- Monitoring entry deployment
- Emergency coordination hub selection
- Organisational coordination cost analysis
- Network compactness evaluation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: numeric
- Can reuse pre‑computed eccentricity for speed

---



---

### 3. center – Graph Center

**Description**  
Returns the set of nodes whose eccentricity equals the graph radius. These are the structural centers.

**Product Value**
- Identifies best global center nodes
- Supports selection of service, monitoring, scheduling, and coordination hubs
- Helps understand central locations in the network

**Typical Scenarios**
- Monitoring node selection
- Service facility layout
- Hub node identification
- Emergency response centre selection
- Network centrality interpretation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: list of center nodes
- Multiple centers may exist

---



---

### 4. periphery – Graph Periphery

**Description**  
Returns the set of nodes whose eccentricity equals the graph diameter. These nodes lie on the structural boundary.

**Product Value**
- Identifies coverage blind spots and remote positions
- Supports edge node, isolated risk point, and structural boundary analysis
- Useful for network optimisation and adding edges

**Typical Scenarios**
- Remote device identification
- Peripheral user analysis
- Transportation terminal analysis
- Supply chain endpoint risk identification
- Network coverage optimisation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: list of periphery nodes
- Often used together with `diameter` and `eccentricity`

---



---

### 5. eccentricity – Eccentricity

**Description**  
For each node, computes the maximum shortest‑path distance to any reachable node. A larger eccentricity means the node is more peripheral.

**Product Value**
- Measures worst‑case reachability distance
- Identifies remote nodes and coverage blind spots
- Foundation for radius, diameter, center, periphery

**Typical Scenarios**
- Network coverage analysis
- Service entry point selection
- Tracking cost estimation
- Remote node identification
- Topology health check

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `dict[node → eccentricity]` or single value
- Note: For disconnected graphs, interpret per component or reachable subgraph

---



---

### 6. wiener_index – Wiener Index

**Description**  
Sum of shortest‑path distances over all unordered node pairs. Measures the global distance cost of the graph.

**Product Value**
- Quantifies overall compactness of the network
- Supports structural comparison between different networks
- Useful for propagation, collaboration, transportation cost evaluation

**Typical Scenarios**
- Total path cost analysis in transportation networks
- Collaboration cost in supply chains
- Compactness comparison of social networks
- Molecular graph structure analysis
- Before/after topology optimisation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: numeric
- Can be computed unweighted or weighted

---



---

### 7. degree_assortativity_coefficient – Degree Assortativity Coefficient

**Description**  
Measures the correlation of degrees between connected nodes.
- `r > 0`: Assortative (high‑degree nodes connect to high‑degree nodes)
- `r < 0`: Disassortative (high‑degree nodes connect to low‑degree nodes)
- `r ≈ 0`: No linear degree preference

**Product Value**
- Determines whether high‑degree nodes cluster together or form a core‑periphery
- Supports connection preference analysis for active accounts
- Identifies core‑periphery structure

**Typical Scenarios**
- Social network assortativity analysis
- Core‑periphery diagnosis
- High‑activity account connection preference
- Biological network connection patterns
- Transaction network hierarchical structure analysis

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: float correlation coefficient
- For directed graphs, can specify in‑ or out‑degree for source/target

---



---

### 8. attribute_assortativity_coefficient – Attribute Assortativity Coefficient

**Description**  
Measures whether nodes with the same categorical attribute value are more likely to be connected.
- `r > 0`: Homophily (same attribute connects)
- `r < 0`: Heterophily (different attributes connect)
- `r ≈ 0`: Weak preference

**Product Value**
- Diagnoses homophily/heterophily in the network
- Supports analysis of connection preferences for departments, types, risk levels
- Useful for community, organisation, and risk structure analysis

**Typical Scenarios**
- Intra‑ / inter‑department collaboration analysis
- Risk‑level homophily analysis
- User type connection preference
- Institutional hierarchical connectivity
- Community homophily evaluation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Required parameter: `attribute`
- Output: float

---



---

### 9. efficiency

**Description**
Compute the efficiency between pairs of nodes in a graph, defined as the multiplicative inverse of the shortest path distance. Higher efficiency means information or influence travels more easily between two nodes. If two nodes are unreachable, efficiency is 0.

**Product Value**

* Quantify how easily information propagates between nodes
* Support local connectivity and fault-tolerance analysis
* Enable cross-network reachability comparison

**Typical Scenarios**

* Social network information propagation efficiency
* Biological network connection resilience assessment
* Transportation network accessibility comparison
* Infrastructure network redundancy analysis
* Before/after network optimization evaluation

**Applicability & Characteristics**

* Graph type: undirected
* Output: pairwise efficiency value or global average efficiency
* Note: edge weights are ignored; all edges treated as equal


---

### 10. average_neighbor_degree – Average Neighbor Degree

**Description**  
For each node, computes the average degree of its neighbors. Indicates whether a node is connected to high‑activity or low‑activity neighbors.

**Product Value**
- Assesses the quality of neighbors a node connects to
- Identifies “core‑attached” or “peripheral” nodes
- A useful local structural feature

**Typical Scenarios**
- Auxiliary node influence assessment
- Social network neighbor quality analysis
- Fraud network peripheral node identification
- Transaction partner activity analysis
- Network hierarchical structure modelling

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `dict[node → average_neighbor_degree]`
- Can use `source`, `target`, `weight` parameters

---



---

### 11. average_degree_connectivity – Average Degree Connectivity

**Description**  
Groups nodes by their degree and computes, for each degree value, the average degree of their neighbors. Shows what degrees nodes of a given degree tend to connect to.

**Product Value**
- Reveals mixing patterns between degree layers
- Indicates assortativity or disassortativity at the degree‑level
- Suitable for plotting degree connectivity curves

**Typical Scenarios**
- Analysis of whether high‑degree nodes connect to high‑degree nodes
- Core‑periphery pattern identification
- Network hierarchical structure analysis
- Activity connection preference in social networks
- Degree‑related patterns in biological networks

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: `dict[degree → average_neighbor_degree]`
- Often interpreted together with assortativity coefficients

---



---

### 12. communicability

**Description**
Quantify the communication strength between node pairs by considering all walks of different lengths, not just shortest paths. From a spectral perspective, communicability C(u, v) = sum_j [phi_j(u) * phi_j(v) * exp(lambda_j)], integrating contributions from walks of all lengths.

**Product Value**

* Provide a more comprehensive proximity measure than shortest paths
* Capture latent interactions and indirect relationships
* Suitable for propagation potential analysis in complex networks

**Typical Scenarios**

* Social network latent interaction discovery
* Biological network protein functional association
* Transportation network overall accessibility
* Complex system global information propagation
* Node similarity and graph kernel methods

**Applicability & Characteristics**

* Graph type: simple undirected graph
* Output: nested dictionary dict[u][v] -> communicability_value
* Complexity: dominated by adjacency matrix spectral decomposition; suitable for small-to-medium networks


---

### 13. node_redundancy

**Description**
Compute the redundancy coefficient of nodes in a bipartite graph, measuring how much a node's neighbors are already interconnected through other nodes. A higher redundancy coefficient means the node's removal has less impact on connectivity.

**Product Value**

* Identify redundant nodes with minimal unique connectivity contribution
* Support bottleneck detection and structural optimization
* Enable recommendation diversity improvement

**Typical Scenarios**

* Recommendation system user preference overlap analysis
* Co-authorship network repeated collaboration detection
* Project network team resource optimization
* Ecological network keystone species identification
* Supply chain redundancy analysis

**Applicability & Characteristics**

* Graph type: bipartite graph
* Output: dict[node -> redundancy_value], range [0, 1]
* Complexity: O(sum_v deg(v)^2)


---

### 14. closeness_vitality

**Description**
Evaluate a node's structural importance by measuring how much the total pairwise distance in the network changes after removing that node. A larger vitality value indicates greater contribution to maintaining global compactness.

**Product Value**

* Identify nodes most critical for global distance efficiency
* Support critical infrastructure and hub node assessment
* Enable network vulnerability and resilience analysis

**Typical Scenarios**

* Transportation network critical station identification
* Communication network core node assessment
* Infrastructure network vulnerability analysis
* Dependency graph critical module identification
* Citation/collaboration network global reachability analysis

**Applicability & Characteristics**

* Graph type: strongly connected graph
* Output: dict[node -> vitality_value] or single-node value
* Note: removing a node may disconnect the graph, resulting in negative infinity
* Can reuse pre-computed wiener_index for acceleration


---

### 15. non_randomness

**Description**
Quantify how much a graph's structure deviates from a random graph. Returns two values: total non-randomness and relative non-randomness. Relative non-randomness closer to 0 indicates the graph resembles a random graph; larger values indicate stronger structural regularity or community organization.

**Product Value**

* Determine whether a network exhibits meaningful structural organization
* Support community structure strength assessment
* Distinguish real networks from random connectivity

**Typical Scenarios**

* Social network community organization assessment
* Network structural randomness comparison
* Pre/post perturbation structural change analysis
* Graph structural pattern recognition
* Weighted edge impact on structural regularity analysis

**Applicability & Characteristics**

* Graph type: undirected, connected, no self-loops, non-empty
* Output: (non-randomness, relative non-randomness) tuple
* Optional community count k; auto-estimated if not specified


---

### 16. rich_club_coefficient

**Description**
Compute the edge density among nodes with degree greater than k for each threshold k. If high-degree nodes are unusually densely connected, a "rich club" phenomenon exists. Can be normalized against a random graph to assess statistical significance.

**Product Value**

* Determine whether core nodes form a tightly-knit group
* Support hierarchical structure and elite core analysis
* Enable identification of network structural backbone

**Typical Scenarios**

* Scientific collaboration network prolific author interconnection
* Autonomous system network top-tier core assessment
* Brain network hub region connectivity analysis
* Airline network hub airport interconnection
* Protein interaction network high-degree protein analysis

**Applicability & Characteristics**

* Graph type: undirected, no parallel edges or self-loops
* Output: dict[k -> phi(k)]
* Optional normalization (comparison with same-degree-distribution random graph)
* Normalization uses Q * m double-edge swaps to generate null model


---

### 17. reciprocity

**Description**
Measure the tendency of node pairs in a directed network to form mutual connections. Global reciprocity is the fraction of edges that have a reciprocal counterpart. Node-level reciprocity measures the symmetry of incoming and outgoing edges.

**Product Value**

* Assess symmetry of relationships in directed networks
* Support hierarchical vs. peer structure differentiation
* Evaluate balance of social interactions

**Typical Scenarios**

* Social platform mutual follow analysis
* Email/messaging network communication symmetry
* International trade network bilateral relationship analysis
* Trust/rating network reciprocity quantification
* Ecological food web species mutual interaction analysis

**Applicability & Characteristics**

* Graph type: directed graph (non-multigraph)
* Output: global float or node-level dict[node -> reciprocity]
* Note: reciprocity is undefined for isolated nodes


---

### 18. resistance_distance – Resistance Distance

**Description**  
Treats the graph as an electrical network and computes the equivalent resistance distance between two nodes. It accounts for multiple parallel paths (redundancy) in addition to the shortest path.

**Product Value**
- Measures global connection strength between nodes
- Reflects path redundancy influence on proximity
- More nuanced than simple shortest‑path distance

**Typical Scenarios**
- Robustness analysis
- Power grid / communication network structure analysis
- Node similarity measurement
- Multi‑path redundancy assessment
- Graph kernel feature construction

**Applicability & Characteristics**
- Graph type: Typically undirected, connected
- Output: numeric resistance distance
- Smaller distance means stronger overall connectivity

---



---

### 19. flow_hierarchy

**Description**
Compute the fraction of edges in a directed graph that do not participate in cycles, measuring the degree of hierarchical organization. Higher values indicate a structure closer to a strict DAG; lower values indicate more feedback loops.

**Product Value**

* Quantify hierarchical degree of directed networks
* Assess whether dependencies approach DAG structure
* Analyze command chains, citation chains, and hierarchical structures

**Typical Scenarios**

* Citation network hierarchical structure analysis
* Command chain hierarchy assessment
* Dependency graph cycle detection
* Supply chain hierarchy analysis
* Influence network feedback loop quantification

**Applicability & Characteristics**

* Graph type: directed graph / multi-directed graph
* Output: float, range [0, 1]
* Complexity: based on strongly connected components, approximately O(m)
* Optional edge weight parameter


---

### 20. barycenter – Graph Barycenter

**Description**  
Returns the set of nodes that minimise the sum of shortest‑path distances to all other nodes. Unlike `center` (which minimises worst‑case distance), `barycenter` minimises total distance cost.

**Product Value**
- Selects nodes with smallest average access cost
- Suitable for logistics hubs, coordination centres, or service placement
- Complements `center` by considering total cost instead of worst case

**Typical Scenarios**
- Warehouse / logistics center location
- Organisational coordination hub analysis
- Network service node selection
- Average access cost optimisation
- Road network center identification

**Applicability & Characteristics**
- Graph type: Typically for connected graphs
- Output: list of barycenter nodes
- Can use `weight` for weighted shortest paths

---



---

### 21. estrada_index

**Description**
Compute the Estrada index of a simple undirected graph by summing the exponentials of the adjacency matrix eigenvalues. It is a spectral descriptor reflecting global compactness, communicability, and structural folding.

**Product Value**

* Provide a single scalar measure of spectral compactness
* Support cross-network global structure comparison
* Commonly used in molecular graph analysis and network topology comparison

**Typical Scenarios**

* Molecular graph structural compactness comparison
* Protein network global structure comparison
* Infrastructure network reinforcement effect evaluation
* Candidate graph structure ranking
* Network evolution trend tracking

**Applicability & Characteristics**

* Graph type: simple undirected graph
* Output: float
* Complexity: O(n^3), dominated by matrix eigendecomposition


---

### 22. girth

**Description**
Return the length of the shortest cycle in the graph. If the graph is acyclic (e.g., a tree or forest), the result is infinity. Girth measures the minimum scale of cyclic dependency in a graph.

**Product Value**

* Determine whether short cycles exist in a graph
* Support tree-like vs. cyclic graph differentiation
* Enable local cyclic structure comparison across networks

**Typical Scenarios**

* Fraud transaction network shortest suspicious loop detection
* Power grid topology shortest loop analysis
* Social rumor network short cycle acceleration assessment
* Graph local cycle comparison
* Dependency graph cyclic dependency shortest path analysis

**Applicability & Characteristics**

* Graph type: undirected graph
* Output: integer or math.inf
* Complexity: O(nm)


---

### 23. harmonic_diameter – Harmonic Diameter

**Description**  
Uses the harmonic mean of pairwise distances to measure the effective distance scale of the graph. More robust to extreme distances or disconnected components than ordinary diameter.

**Product Value**
- Provides a robust global distance measure
- Suitable for graphs that are not fully connected or have local fractures
- Allows comparison of overall reachability efficiency

**Typical Scenarios**
- Large‑scale social network distance analysis
- Effective distance in disconnected graphs
- Compactness comparison before/after network evolution
- Propagation efficiency assessment
- Topology optimisation evaluation

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: numeric
- Focuses on overall effective distance rather than the single farthest pair

---



---

### 24. kemeny_constant – Kemeny Constant

**Description**  
The Kemeny constant of a random walk on the graph. It measures the expected time to reach stationarity, averaged over starting nodes.

**Product Value**
- Quantifies mixing efficiency of random walks
- Supports diffusion, access, and convergence analysis
- Serves as a global reachability and stability measure

**Typical Scenarios**
- Random walk model analysis
- Web page access modelling
- Random walk‑based recommendation recall
- Diffusion process stability analysis
- Markov chain network evaluation

**Applicability & Characteristics**
- Graph type: Typically connected graphs (or suitable directed graphs)
- Output: numeric
- Related to random walks, transition matrices, and stationary analysis

---



---
## V. Recommended Usage Guide

### 1. Distance and Span Analysis

* Compute node farthest distance: `eccentricity`
* Compute network maximum span: `diameter`
* Compute optimal center's worst-case distance: `radius`
* Find structural center nodes: `center`
* Find structural peripheral nodes: `periphery`
* Measure overall distance cost: `wiener_index`
* Use a more robust effective distance scale: `harmonic_diameter`

### 2. Efficiency and Communicability Analysis

* Compute pairwise efficiency: `efficiency`
* Quantify comprehensive communication strength: `communicability`
* Compute resistance distance between nodes: `resistance_distance`

### 3. Center Position and Global Importance

* Find minimum total distance node: `barycenter`
* Evaluate node contribution to global compactness: `closeness_vitality`
* Compute global spectral compactness: `estrada_index`

### 4. Assortativity and Connection Preference Analysis

* Determine whether high-degree nodes interconnect: `degree_assortativity_coefficient`
* Determine whether same-category nodes homophilically connect: `attribute_assortativity_coefficient`
* View average degree of each node's neighbors: `average_neighbor_degree`
* View average neighbor degree by degree group: `average_degree_connectivity`

### 5. Structural Regularity and Hierarchy Analysis

* Determine deviation from random graph structure: `non_randomness`
* Determine whether high-degree nodes form a tight group: `rich_club_coefficient`
* Measure hierarchical degree of directed networks: `flow_hierarchy`
* Measure tendency toward mutual connections: `reciprocity`

### 6. Cycle and Redundancy Analysis

* Find shortest cycle length: `girth`
* Analyze indirect neighbor interconnection in bipartite graphs: `node_redundancy`

### 7. Random Walk and Steady-State Analysis

* Analyze random walk steady-state access characteristics: `kemeny_constant`

---

## VI. Typical Directly Answerable Questions

* "What is the maximum span of this network?"
* "Which nodes are at the network center?"
* "Which nodes are most remote and hardest to reach?"
* "What is the overall distance cost of the network?"
* "Which node has the minimum total distance to all others?"
* "Is this network compact?"
* "What is the resistance distance between two nodes?"
* "How efficient is the random walk on this network overall?"
* "Do high-degree nodes preferentially connect to other high-degree nodes?"
* "Do high-degree nodes form a tightly connected group (rich club)?"
* "Is this graph structure meaningfully organized or close to random?"
* "What fraction of edges in the directed network avoid cycles (hierarchy level)?"
* "What is the proportion of bidirectional connections in the directed network?"
* "How long is the shortest cycle in the graph?"
* "What is the global spectral compactness (Estrada index) of this graph?"
* "How much does total pairwise distance change after removing a node?"
* "How indirectly interconnected are a node's neighbors in the bipartite graph?"
* "What is the comprehensive communication strength between node pairs (beyond shortest paths)?"
* "What is the efficiency (inverse shortest path) between node pairs?"

---

## VII. Engineering and Usage Notes

1. **Connectivity Requirements**
   Distance metrics such as `eccentricity`, `radius`, `diameter`, `center`, `periphery`, `barycenter`, `wiener_index`, and `closeness_vitality` should be interpreted carefully in disconnected graphs.

   * Undirected graphs: compute per connected component or use the largest component
   * Directed graphs: use strongly connected components or reachability scope

2. **Weighted Distance Semantics**
   `weight` typically represents distance, cost, time, or impedance.
   If edge weights represent capacity, similarity, or strength (higher is better), they should not be used directly as distances.

3. **Performance Recommendations**

   * When computing `eccentricity`, `radius`, `diameter`, `center`, `periphery` together, pre-compute and reuse `eccentricity`
   * `communicability`, `non_randomness`, `estrada_index` involve matrix spectral decomposition; use cautiously on large graphs
   * `rich_club_coefficient` normalized mode requires generating a random null model at higher computational cost
   * `closeness_vitality` requires multiple shortest-path computations; pre-compute `wiener_index` to accelerate

4. **Directed Graph Operators**
   `reciprocity` and `flow_hierarchy` apply only to directed graphs; calling them on undirected graphs is not meaningful.

5. **Bipartite Graph Operators**
   `node_redundancy` is designed for bipartite graphs; note semantic implications when used on general graphs.

---

## VIII. Operator List

| No. | Operator Name | Description |
| ---:| --- | --- |
| 1 | `diameter` | Compute graph diameter |
| 2 | `radius` | Compute graph radius |
| 3 | `center` | Get graph center node set |
| 4 | `periphery` | Get graph periphery node set |
| 5 | `eccentricity` | Compute node eccentricity |
| 6 | `wiener_index` | Compute Wiener index |
| 7 | `degree_assortativity_coefficient` | Compute degree assortativity coefficient |
| 8 | `attribute_assortativity_coefficient` | Compute attribute assortativity coefficient |
| 9 | `efficiency` | Compute pairwise efficiency |
| 10 | `average_neighbor_degree` | Compute average neighbor degree |
| 11 | `average_degree_connectivity` | Compute average degree connectivity |
| 12 | `communicability` | Compute communicability |
| 13 | `node_redundancy` | Compute node redundancy |
| 14 | `closeness_vitality` | Compute closeness vitality |
| 15 | `non_randomness` | Compute non-randomness |
| 16 | `rich_club_coefficient` | Compute rich-club coefficient |
| 17 | `reciprocity` | Compute reciprocity |
| 18 | `resistance_distance` | Compute resistance distance |
| 19 | `flow_hierarchy` | Compute flow hierarchy |
| 20 | `barycenter` | Get graph barycenter node set |
| 21 | `estrada_index` | Compute Estrada index |
| 22 | `girth` | Compute girth |
| 23 | `harmonic_diameter` | Compute harmonic diameter |
| 24 | `kemeny_constant` | Compute Kemeny constant |
