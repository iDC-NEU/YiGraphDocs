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

| Capability Type                     | Corresponding Operator                           | Description                                                        |
|-------------------------------------|--------------------------------------------------|--------------------------------------------------------------------|
| Node farthest distance              | `eccentricity`                                   | Distance to farthest reachable node                                 |
| Graph radius                        | `radius`                                         | Minimum eccentricity in the graph                                   |
| Graph diameter                      | `diameter`                                       | Maximum eccentricity in the graph                                   |
| Graph center                        | `center`                                         | Set of nodes with minimum eccentricity                              |
| Graph periphery                     | `periphery`                                      | Set of nodes with eccentricity equal to diameter                    |
| Graph barycenter                    | `barycenter`                                     | Set of nodes with minimum sum of shortest‑path distances            |
| Harmonic diameter                   | `harmonic_diameter`                              | Effective distance scale using harmonic mean                        |
| Wiener index                        | `wiener_index`                                   | Sum of all‑pairs shortest‑path distances                            |
| Resistance distance                 | `resistance_distance`                            | Resistance distance between node pairs                              |
| Effective graph resistance          | `effective_graph_resistance`                     | Global effective resistance of the graph                            |
| Kemeny constant                     | `kemeny_constant`                                | Mean hitting time to stationarity for random walks                  |
| Degree assortativity coefficient    | `degree_assortativity_coefficient`               | Correlation of degrees between connected nodes                      |
| Degree Pearson correlation          | `degree_pearson_correlation_coefficient`         | Pearson correlation of degrees across edges                         |
| Attribute assortativity coefficient | `attribute_assortativity_coefficient`            | Homophily/heterophily for categorical attributes                    |
| Numeric assortativity coefficient   | `numeric_assortativity_coefficient`              | Correlation of numeric attributes across edges                      |
| Average neighbor degree             | `average_neighbor_degree`                        | Average degree of neighbors for each node                           |
| Average degree connectivity         | `average_degree_connectivity`                    | For each degree, average neighbor degree                            |
| Attribute mixing matrix             | `attribute_mixing_matrix`                        | Matrix of connections between attribute classes                     |
| Degree mixing matrix                | `degree_mixing_matrix`                           | Matrix of connections between degree values                         |
| Attribute mixing dictionary         | `attribute_mixing_dict`                          | Dictionary version of attribute mixing                              |
| Degree mixing dictionary            | `degree_mixing_dict`                             | Dictionary version of degree mixing                                 |
| Generic mixing dictionary           | `mixing_dict`                                    | Build mixing dictionary from arbitrary endpoint value pairs         |
| Node attribute XY generator         | `node_attribute_xy`                              | Generate (attribute value, attribute value) pairs for each edge     |
| Node degree XY generator            | `node_degree_xy`                                 | Generate (degree, degree) pairs for each edge                       |

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

### 1. eccentricity – Eccentricity

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

### 3. diameter – Graph Diameter

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

### 4. center – Graph Center

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

### 5. periphery – Graph Periphery

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

### 6. barycenter – Graph Barycenter

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

### 7. harmonic_diameter – Harmonic Diameter

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

### 8. wiener_index – Wiener Index

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

### 9. resistance_distance – Resistance Distance

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

### 10. effective_graph_resistance – Effective Graph Resistance

**Description**  
Computes the total effective resistance of the whole graph, a global measure of the graph’s resistance (inverse of robustness).

**Product Value**
- Measures overall connectivity resilience
- Reflects path redundancy and structural tightness
- Allows robustness comparison across networks

**Typical Scenarios**
- Power network resilience analysis
- Communication network robustness assessment
- Transportation network redundancy analysis
- Graph structure optimisation comparison
- Network vulnerability analysis

**Applicability & Characteristics**
- Graph type: Typically undirected, connected
- Output: numeric
- Lower values indicate tighter connectivity and stronger redundancy

---

### 11. kemeny_constant – Kemeny Constant

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

### 12. degree_assortativity_coefficient – Degree Assortativity Coefficient

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

### 13. degree_pearson_correlation_coefficient – Degree Pearson Correlation Coefficient

**Description**  
Pearson correlation of degrees across edges. Equivalent to degree assortativity in the undirected case.

**Product Value**
- Standard correlation view of degree mixing
- Suitable for statistical analysis and cross‑network comparison
- Can be cross‑checked with degree assortativity

**Typical Scenarios**
- Degree correlation analysis
- Statistical modelling of network structure
- High‑degree node connection preference
- Core‑periphery identification
- Assortativity comparison across networks

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: float
- Interpretation similar to degree assortativity coefficient

---

### 14. attribute_assortativity_coefficient – Attribute Assortativity Coefficient

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

### 15. numeric_assortativity_coefficient – Numeric Assortativity Coefficient

**Description**  
Measures whether the numeric attribute values (e.g., age, score, risk level) of connected nodes are correlated (Pearson correlation).

**Product Value**
- Supports connection preference analysis for continuous or ordinal attributes
- Determines whether similar numeric values tend to connect
- Suitable for risk, grade, activity, size, etc.

**Typical Scenarios**
- Do accounts with similar risk scores connect?
- Do users of similar ages follow each other?
- Do nodes with similar activity levels cluster?
- Do companies of similar size trade?
- Node score vs connection preference analysis

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Required parameter: `attribute`
- Output: float correlation

---

### 16. average_neighbor_degree – Average Neighbor Degree

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

### 17. average_degree_connectivity – Average Degree Connectivity

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

### 18. attribute_mixing_matrix – Attribute Mixing Matrix

**Description**  
Constructs a matrix where rows and columns correspond to attribute values, and entries count (or proportion) of edges between attribute classes.

**Product Value**
- Converts attribute connection preference into a matrix
- Supports quantitative analysis of inter‑class connection frequencies
- Foundational for assortativity and homophily analysis

**Typical Scenarios**
- Inter‑department collaboration matrix
- Risk level connection matrix
- User type interaction matrix
- Industry transaction relation matrix
- Community connection preference analysis

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Required parameter: `attribute`
- Output: matrix (list of lists or numpy array)
- Can be normalized

---

### 19. degree_mixing_matrix – Degree Mixing Matrix

**Description**  
Constructs a matrix where rows and columns correspond to degree values, and entries count (or proportion) of edges between nodes of those degrees.

**Product Value**
- Shows connection patterns across degree layers
- Supports assortativity and core‑periphery analysis
- Useful for statistical modelling of network structure

**Typical Scenarios**
- High‑degree node connection preference analysis
- Core‑periphery connectivity pattern analysis
- Visualisation of assortativity/disassortativity
- Hierarchical degree structure modelling
- Topology comparison

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: matrix
- For directed graphs, can specify `x` and `y` degree types

---

### 20. attribute_mixing_dict – Attribute Mixing Dictionary

**Description**  
Dictionary representation of attribute mixing, with keys as `(attr_u, attr_v)` and values as counts or proportions. Sparse version of the mixing matrix.

**Product Value**
- Easy inspection of specific attribute pair connections
- Suitable for sparse categories or rule extraction
- Lightweight alternative to mixing matrix

**Typical Scenarios**
- Category connection frequency counting
- Department‑department connection lookup
- Risk type interaction analysis
- Multi‑label node relation analysis
- Attribute preference rule extraction

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Required parameter: `attribute`
- Output: nested dictionary
- Can be normalized

---

### 21. degree_mixing_dict – Degree Mixing Dictionary

**Description**  
Dictionary representation of degree mixing, keys as `(deg_u, deg_v)` with counts or proportions.

**Product Value**
- Direct view of degree‑pair connection distribution
- Supports assortativity, disassortativity, and hierarchical analysis
- Convenient for custom statistics or rule extraction

**Typical Scenarios**
- Degree‑layer connection statistics
- High‑low degree connection analysis
- Core‑periphery structural interpretation
- Network generation model parameter analysis
- Topological connection preference modelling

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: nested dictionary
- For directed graphs, can specify degree types for source/target

---

### 22. mixing_dict – Generic Mixing Dictionary

**Description**  
Given a sequence of endpoint value pairs (generated from edges), builds a mixing dictionary. A generic tool underlying attribute and degree mixing.

**Product Value**
- Supports custom mixing statistics beyond attributes/degrees
- Extensible for other node properties
- Builds custom structural indicators

**Typical Scenarios**
- Custom category mixing statistics
- Edge endpoint label pair counting
- Connection preference rule generation
- Graph feature engineering
- Relational pattern modelling

**Applicability & Characteristics**
- Input: Iterable of `(value_u, value_v)` pairs
- Output: mixing dictionary
- Optional normalisation

---

### 23. node_attribute_xy – Node Attribute XY Generator

**Description**  
Generates, for each edge, the pair of attribute values of its two endpoints. Used as input for attribute mixing matrix/dictionary or custom assortativity calculations.

**Product Value**
- Provides attribute pairing data for further analysis
- Supports custom connection preference analysis
- Data preparation step for downstream statistics

**Typical Scenarios**
- Generating department‑department pairs
- Risk level edge pairs
- User type interaction pairs
- Custom attribute assortativity computation
- Preprocessing for mixing matrix construction

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Required parameter: `attribute`
- Output: iterator of `(attr_u, attr_v)` pairs

---

### 24. node_degree_xy – Node Degree XY Generator

**Description**  
Generates, for each edge, the pair of degrees of its two endpoints. Used as input for degree mixing matrix/dictionary or custom degree correlation analysis.

**Product Value**
- Provides degree pairing data for analysis
- Supports custom degree‑based statistics
- Foundation for degree mixing and assortativity

**Typical Scenarios**
- High‑degree node connection pattern analysis
- Custom degree assortativity calculation
- Degree mixing matrix construction
- Core‑periphery structure analysis
- Graph statistical feature engineering

**Applicability & Characteristics**
- Graph type: Directed / Undirected
- Output: iterator of `(deg_u, deg_v)` pairs
- For directed graphs, can specify `x` and `y` degree types

---

## V. Recommended Usage Guide

### 1. Distance and Span Analysis
- Node farthest distance: `eccentricity`
- Maximum network span: `diameter`
- Best‑center worst‑case distance: `radius`
- Structural center nodes: `center`
- Structural periphery nodes: `periphery`
- Overall distance cost: `wiener_index`
- Robust effective distance: `harmonic_diameter`

### 2. Centrality and Overall Cost
- Nodes with smallest total distance: `barycenter`
- Nodes with smallest worst‑case distance: `center`
- Compare global distance cost: `wiener_index`
- Average‑sense reachability efficiency: `harmonic_diameter`

### 3. Graph Resistance and Random Walk
- Pairwise resistance distance: `resistance_distance`
- Global effective resistance: `effective_graph_resistance`
- Random‑walk mixing efficiency: `kemeny_constant`

### 4. Assortativity and Connection Preference
- High‑degree clustering: `degree_assortativity_coefficient`
- Degree Pearson correlation: `degree_pearson_correlation_coefficient`
- Categorical homophily: `attribute_assortativity_coefficient`
- Numeric attribute correlation: `numeric_assortativity_coefficient`

### 5. Neighbor Degree Structure
- Node‑level average neighbor degree: `average_neighbor_degree`
- Degree‑level average neighbor degree: `average_degree_connectivity`

### 6. Mixing Matrices and Dictionaries
- Attribute mixing matrix: `attribute_mixing_matrix`
- Degree mixing matrix: `degree_mixing_matrix`
- Attribute mixing dictionary: `attribute_mixing_dict`
- Degree mixing dictionary: `degree_mixing_dict`
- Custom mixing: `mixing_dict`

### 7. Edge Endpoint Pair Generation
- Attribute value pairs: `node_attribute_xy`
- Degree value pairs: `node_degree_xy`

---

## VI. Typical Questions That Can Be Directly Answered

- “What is the maximum span of this network?”
- “Which nodes are at the network center?”
- “Which nodes are most remote and hardest to cover?”
- “From the optimal center, what is the worst case distance?”
- “What is the total distance cost of the whole network?”
- “Which node has the smallest sum of distances to all others?”
- “Is this network compact?”
- “What is the resistance distance between two nodes?”
- “Is the effective graph resistance high or low?”
- “How efficient is random walk mixing on this graph?”
- “Do high‑degree nodes tend to connect to high‑degree nodes?”
- “Does the network exhibit core‑periphery structure?”
- “Do nodes of the same department/type/risk level connect more?”
- “Are nodes with similar numeric attributes more likely to connect?”
- “What is the average neighbor degree for each node?”
- “What degrees do nodes of a given degree connect to?”
- “What is the mixing matrix between attribute classes?”
- “What is the mixing matrix between degree layers?”
- “For each edge, how are the attribute values paired?”
- “For each edge, how are the degrees paired?”

---

## VII. Engineering and Usage Notes

1. **Connectivity Requirements**
   Distance metrics (`eccentricity`, `radius`, `diameter`, `center`, `periphery`, `barycenter`, `wiener_index`) require careful interpretation for disconnected graphs.
   - Undirected: compute per connected component or take the largest component.
   - Directed: consider strongly connected components or interpret within reachable subgraphs.

2. **Weighted Distance Semantics**
   `weight` should represent distance, cost, time, or impedance. If edge weights represent capacity, similarity, or strength (larger = better), transform them appropriately (e.g., reciprocal or negative log, ensuring positivity).

3. **Interpreting Assortativity**
   Assortativity coefficients show correlation, not causation.
   - `r > 0`: like connects to like.
   - `r < 0`: opposite connects.
   - `r ≈ 0`: no linear preference.

4. **Mixing Matrices and Dictionaries**
   - Matrices are good for visualisation and numerical computations.
   - Dictionaries are good for sparse categories, rule extraction, and direct lookup.
   - Normalisation changes interpretation (frequency vs. probability).

5. **Performance Recommendations**
   - When computing `eccentricity`, `radius`, `diameter`, `center`, `periphery` together, pre‑compute and reuse `eccentricity`.
   - All‑pairs shortest‑path based metrics are expensive on large graphs; consider sampling, component analysis, or approximation.
   - Assortativity and mixing measures are generally much lighter.

---

## VIII. Operator List

| No. | Operator Name                                   | Description                                   |
|-----|-------------------------------------------------|-----------------------------------------------|
| 1   | `eccentricity`                                  | Compute node eccentricity                     |
| 2   | `radius`                                        | Compute graph radius                          |
| 3   | `diameter`                                      | Compute graph diameter                        |
| 4   | `center`                                        | Get graph center nodes                        |
| 5   | `periphery`                                     | Get graph periphery nodes                     |
| 6   | `barycenter`                                    | Get graph barycenter nodes                    |
| 7   | `harmonic_diameter`                             | Compute harmonic diameter                     |
| 8   | `wiener_index`                                  | Compute Wiener index                          |
| 9   | `resistance_distance`                           | Compute resistance distance between nodes     |
| 10  | `effective_graph_resistance`                    | Compute effective graph resistance            |
| 11  | `kemeny_constant`                               | Compute Kemeny constant                       |
| 12  | `degree_assortativity_coefficient`              | Compute degree assortativity coefficient      |
| 13  | `degree_pearson_correlation_coefficient`        | Compute degree Pearson correlation            |
| 14  | `attribute_assortativity_coefficient`           | Compute attribute assortativity coefficient   |
| 15  | `numeric_assortativity_coefficient`             | Compute numeric assortativity coefficient     |
| 16  | `average_neighbor_degree`                       | Compute average neighbor degree               |
| 17  | `average_degree_connectivity`                   | Compute average degree connectivity           |
| 18  | `attribute_mixing_matrix`                       | Build attribute mixing matrix                 |
| 19  | `degree_mixing_matrix`                          | Build degree mixing matrix                    |
| 20  | `attribute_mixing_dict`                         | Build attribute mixing dictionary             |
| 21  | `degree_mixing_dict`                            | Build degree mixing dictionary                |
| 22  | `mixing_dict`                                   | Build generic mixing dictionary               |
| 23  | `node_attribute_xy`                             | Generate node attribute XY pairs              |
| 24  | `node_degree_xy`                                | Generate node degree XY pairs                 |