---
sidebar_position: 13
title: Cliques & Cores Operator Set
---

# Cliques & Cores Operator Set

**Operator Category**: Cliques & Cores (clique discovery, core decomposition, and high‑cohesion structure analysis)

**Number of Algorithms**: 8

**Applicable Stages**: Dense group discovery, maximum weight clique identification, core member identification, network backbone extraction, core‑periphery hierarchical analysis, peripheral structure analysis, critical node identification at core boundaries, triangle‑supported structure analysis

**Product Positioning**: Provides a unified capability foundation for “which dense cliques exist in the network / who is in the core layer / what remains after stripping away the periphery / which nodes are on the edge or in a critical layer / which relationships have stronger triangular support”.

---

## I. Operator Set Overview

The Cliques & Cores operator set focuses on **highly cohesive substructure discovery** and **core‑periphery hierarchical analysis**, addressing the following key questions:

1. **Clique Discovery**
   - Does there exist a set of nodes in the network where every pair is directly connected?
   - What are the maximal cliques that cannot be extended further?
   - When node weights are considered, which clique has the highest total value?

2. **Core Hierarchy Identification**
   - In which core layer (k‑core level) does each node reside?
   - For a given k, what is the stable core structure that remains?
   - Which nodes belong to the core and which to the periphery?

3. **Core‑Periphery Structure Analysis**
   - Which nodes belong to a specific k‑shell?
   - What is the peripheral crust structure?
   - Which nodes in the k‑core are just at the critical support boundary (k‑corona)?

4. **Higher‑Order Cohesive Structure Analysis**
   - Which edges are supported by a sufficient number of triangles?
   - Are there stable structures that go beyond k‑core and emphasize local triangular closure?
   - Which subgraphs exhibit higher relationship credibility and structural robustness?

5. **Complementary Analysis of Cliques, Cores, and Trusses**
   - Cliques emphasize **complete connectivity**, suitable for discovering small but strong tight groups.
   - Cores emphasize **minimum degree constraints**, suitable for discovering large‑scale stable backbones.
   - Trusses emphasize **triangular support**, suitable for discovering even higher‑order cohesive structures.

---

## II. Operator Capability Classification

| Capability Type                | Corresponding Operator   | Description                                                   |
|--------------------------------|--------------------------|---------------------------------------------------------------|
| Maximal clique enumeration     | `find_cliques`           | Enumerate all maximal cliques in the graph                    |
| Maximum weight clique          | `max_weight_clique`      | Find a clique with the largest sum of node weights            |
| Core number computation        | `core_number`            | Compute the core number for each node                         |
| K‑Core extraction              | `k_core`                 | Extract the k‑core subgraph                                   |
| K‑Shell extraction             | `k_shell`                | Extract the k‑shell (nodes with exactly core number = k)      |
| K‑Crust extraction             | `k_crust`                | Extract the subgraph with core number ≤ k (the periphery)     |
| K‑Corona extraction            | `k_corona`               | Extract nodes in the k‑core that have degree exactly k        |
| K‑Truss extraction             | `k_truss`                | Extract the k‑truss (edges supported by at least k‑2 triangles) |

---

## III. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph
- **Common Input Parameters**:
  - `nodes`: Nodes that must be included (for `find_cliques`)
  - `weight`: Node weight attribute (for `max_weight_clique`)
  - `k`: Core order, shell order, corona order, or truss order
  - `core_number`: Optional precomputed core numbers to speed up some core‑based algorithms
- **Common Output Types**:
  - Clique enumeration: `iterator[list[node]]`
  - Maximum weight clique: clique node list and its total weight
  - Core decomposition: `dict[node → core_index]`
  - Subgraph extraction: `NetworkX Graph`

---

## IV. Detailed Operator Descriptions

### 1. find_cliques – Maximal Clique Enumeration

**Description**  
Enumerates all maximal cliques in the graph. A maximal clique is a set of nodes where every two nodes are adjacent, and no additional node can be added while preserving the clique property.

**Product Value**
- Discovers the most tightly connected small groups in the network
- Supports identification of strong, fully‑connected relationships
- Suitable for discovering small but highly cohesive groups with strong consistency and exclusivity

**Typical Scenarios**
- Close friend circles in social networks
- Fraud ring detection
- Fully collaborative team discovery
- Protein complex analysis
- Highly consistent user group discovery

**Applicability & Characteristics**
- Graph type: Undirected
- Output: Iterator over maximal clique node lists
- Note: Maximal cliques are not necessarily maximum cliques; there can be many of varying sizes
- Complexity: Worst‑case exponential

---

### 2. max_weight_clique – Maximum Weight Clique

**Description**  
Finds a clique with the maximum total node weight. If no weight is specified, nodes are treated equally, which reduces to finding a clique with the largest size (or highest value under default weights).

**Product Value**
- Not only considers clique size but also the total value of the clique
- Suitable for finding “fully compatible and overall highest‑value” node combinations
- Can be used for combinatorial optimisation under strong constraints and high‑value group discovery

**Typical Scenarios**
- High‑value core group identification
- Team member selection with mutual compatibility
- Investment portfolio selection
- Advertising channel combination selection
- Gene module screening

**Applicability & Characteristics**
- Graph type: Undirected
- Key parameter: `weight`
- Output: Node list of the maximum weight clique and its total weight
- Note: Maximum weight clique is NP‑hard; use with caution on large graphs

---

### 3. core_number – Core Number Computation

**Description**  
Computes the core number for each node. The core number of a node is the largest k such that the node belongs to the k‑core (i.e., the highest‑order core layer the node can participate in).

Intuitively:
- Larger core number → node lies deeper in the core of the network
- Smaller core number → node is more peripheral or in a weakly‑connected region

**Product Value**
- Identifies whether a node is in the network core or periphery
- Supports core‑periphery structure analysis
- Provides a basic indicator for network stability, influence, and propagation potential

**Typical Scenarios**
- Core user identification
- Core device identification
- Core paper or author identification
- Network backbone analysis
- Propagation potential assessment

**Applicability & Characteristics**
- Graph type: Undirected / Directed
- Output: `dict[node → core_index]`
- Complexity: Typically `O(V + E)`

---

### 4. k_core – K‑Core Subgraph Extraction

**Description**  
Extracts the k‑core subgraph. The k‑core is the maximal induced subgraph where every node has degree at least k within the subgraph. It is typically obtained by iteratively peeling away nodes with degree less than k.

**Product Value**
- Extracts the stable core backbone of the network
- Removes low‑degree, peripheral, or noisy nodes
- Supports network resilience analysis, backbone extraction, and main structure analysis

**Typical Scenarios**
- Network backbone extraction
- Social core circle identification
- Zombie node filtering
- Supply chain stability structure analysis
- Core topic identification in knowledge networks

**Applicability & Characteristics**
- Graph type: Undirected / Directed
- Key parameter: `k`
- Output: k‑core subgraph
- Complexity: Typically `O(V + E)`

---

### 5. k_shell – K‑Shell Extraction

**Description**  
Extracts the k‑shell for a given k. The k‑shell consists of nodes whose core number is exactly k, representing a specific layer in the core hierarchy.

**Product Value**
- Layers the network by core depth
- Distinguishes node groups at different core levels
- Supports hierarchical analysis of core, quasi‑core, and peripheral nodes

**Typical Scenarios**
- Hierarchical user segmentation
- Visualisation of network core layers
- Propagation layer analysis
- Organisational network layering
- Distinguishing core from quasi‑core members

**Applicability & Characteristics**
- Graph type: Undirected / Directed
- Key parameter: `k`
- Output: k‑shell subgraph
- Features: Focuses on nodes exactly at a given core level

---

### 6. k_crust – K‑Crust Extraction

**Description**  
Extracts the k‑crust, i.e., the subgraph consisting of nodes with core number at most k. It is often used to examine the more peripheral, lower‑core parts of the network.

**Product Value**
- Identifies the peripheral structure of the network
- Supports analysis of peripheral nodes, weakly connected nodes, and low‑stability structures
- Can be combined with k‑core to provide core‑vs‑periphery comparative analysis

**Typical Scenarios**
- Edge user analysis
- Low‑activity node screening
- Peripheral device identification
- Observing peripheral structure before network denoising
- Core‑periphery contrast analysis

**Applicability & Characteristics**
- Graph type: Undirected / Directed
- Key parameter: `k`
- Output: k‑crust subgraph
- Features: Focuses on the lower‑core peripheral structure

---

### 7. k_corona – K‑Corona Extraction

**Description**  
Extracts the k‑corona, i.e., the set of nodes that belong to the k‑core and have degree exactly k within that k‑core. These nodes are critically supported at the boundary of the k‑core.

**Product Value**
- Identifies critical nodes in the core structure
- Supports analysis of the stability boundaries of the core
- Helps determine how easily a core layer can be disrupted

**Typical Scenarios**
- Network vulnerability analysis
- Boundary node identification in the core layer
- Critical node discovery in propagation networks
- Organisational structure stability analysis
- Risk assessment in infrastructure networks

**Applicability & Characteristics**
- Graph type: Undirected / Directed
- Key parameter: `k`
- Output: k‑corona subgraph
- Features: Focuses on nodes that just satisfy the degree‑k constraint inside the k‑core

---

### 8. k_truss – K‑Truss Subgraph Extraction

**Description**  
Extracts the k‑truss subgraph. A k‑truss requires that every edge in the subgraph is supported by at least `k‑2` triangles. Thus, it emphasises local triangular cohesion more strongly than k‑core.

**Product Value**
- Discovers stable groups with strong triangular closure relationships
- More sensitive to edge quality and local support than core decomposition
- Suitable for identifying highly credible and highly cohesive relational structures

**Typical Scenarios**
- Strong community discovery
- Fraud ring detection
- Mutual trust network analysis
- Stable group discovery in collaboration networks
- Extraction of highly cohesive subgraphs

**Applicability & Characteristics**
- Graph type: Typically undirected
- Key parameter: `k`
- Output: k‑truss subgraph
- Features: Emphasises triangular support, suitable for higher‑order cohesive structure analysis

---

## V. Recommended Usage Guide

### 1. Clique Discovery
- Enumerate all maximal cliques: `find_cliques`
- Find the highest‑value fully‑connected group: `max_weight_clique`

### 2. Core Structure Analysis
- Compute core numbers for each node: `core_number`
- Extract the core subgraph for a given k: `k_core`
- Extract a specific shell layer: `k_shell`
- Extract the peripheral crust: `k_crust`
- Extract the critical corona layer: `k_corona`

### 3. Higher‑Order Cohesive Structure Analysis
- Extract a subgraph with strong triangular support: `k_truss`

### 4. Scenario‑Based Selection Advice
- **Find “everyone‑connected‑to‑everyone” tight groups** → `find_cliques`
- **Find the highest‑total‑value fully‑connected combination** → `max_weight_clique`
- **Identify network core members** → `core_number`
- **Extract the network backbone** → `k_core`
- **Observe nodes layer by layer according to core depth** → `k_shell`
- **Analyse peripheral structure** → `k_crust`
- **Find critical nodes at the core boundary** → `k_corona`
- **Find stable groups with strong triangular support** → `k_truss`

---

## VI. Typical Questions That Can Be Directly Answered

- “What fully‑connected small groups exist in the network?”
- “Which nodes belong to each maximal clique?”
- “Under the constraint of full connectivity, which group has the highest total value?”
- “Who are the most central members of the network?”
- “What is the core number of each node?”
- “After peeling away peripheral nodes, what backbone remains?”
- “Which nodes belong to a given k‑shell?”
- “Which nodes are in the peripheral structure?”
- “In the core structure, which nodes are in a critical support state?”
- “Which edges are supported by enough triangles?”
- “Is this node a core member, a quasi‑core member, or a peripheral node?”
- “Is the core‑periphery structure of this network stable?”

---

## VII. Operator List

| No. | Operator Name          | Description                              |
|-----|------------------------|------------------------------------------|
| 1   | `find_cliques`         | Enumerate all maximal cliques            |
| 2   | `max_weight_clique`    | Find a maximum weight clique             |
| 3   | `core_number`          | Compute core numbers for nodes           |
| 4   | `k_core`               | Extract the k‑core subgraph              |
| 5   | `k_shell`              | Extract the k‑shell layer                |
| 6   | `k_crust`              | Extract the k‑crust peripheral subgraph  |
| 7   | `k_corona`             | Extract the k‑corona critical nodes      |
| 8   | `k_truss`              | Extract the k‑truss subgraph             |