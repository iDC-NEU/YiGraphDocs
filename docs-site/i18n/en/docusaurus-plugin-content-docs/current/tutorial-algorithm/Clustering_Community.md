---
sidebar_position: 5
---
# Clustering & Community Operators

**Operator Category**: Clustering & Community (clustering coefficient, community detection, transitivity, cycle structures)

**Number of Algorithms**: 23

**Applicable Stages**: network structure insight, clique/group identification, community quality evaluation, closed-loop/cycle detection, relationship density quantification, graph partitioning optimization, bipartite graph community analysis

**Product Positioning**: Provide a unified operator capability base for answering questions such as: "Does the network have tight subgroups?", "How can we partition people/entities into communities?", "Is this partition good?", "Are there closed loops/cycles?", "How to perform bipartite or multi-community partitioning?", "How to identify triadic structure patterns?"

---

## I. Operator Set Overview

The Clustering & Community operator set covers four major categories:

1. **Local Density & Network Clustering**
   - Are a node’s neighbors connected to each other?
   - How closed are triangles at the network level?
   - Are there cliques, gangs, or locally dense structures?
   - Typical algorithms: `clustering`, `average_clustering`, `triangles`, `transitivity`, `square_clustering`

2. **Community Detection & Graph Partitioning**
   - How to automatically discover communities, factions, or circles without labels?
   - How to perform fast community detection on large-scale networks?
   - How to perform bipartition or partition with a specified number of communities?
   - Typical algorithms: `greedy_modularity_communities`, `naive_greedy_modularity_communities`, `louvain_communities`, `leiden_communities`, `girvan_newman`, `label_propagation_communities`, `asyn_lpa_communities`, `asyn_fluidc`, `kernighan_lin_bisection`

3. **Overlapping Communities & Bipartite Graph Community Analysis**
   - Can a node belong to multiple communities simultaneously?
   - How to perform modularity-based bipartition on bipartite graphs?
   - Typical algorithms: `k_clique_communities`, `spectral_modularity_bipartition`

4. **Cycle Structures & Loop Detection**
   - What are the simple cycles in the graph?
   - What is a cycle basis for an undirected graph?
   - Is there a minimum weight cycle basis?
   - Typical algorithms: `simple_cycles`, `cycle_basis`, `minimum_cycle_basis`

5. **Community Evaluation & Validation**
   - How good is a community partition?
   - Is it a strict partition?
   - Are within-group edges sufficient and between-group edges few?
   - Typical algorithms: `modularity`, `partition_quality`, `is_partition`

6. **Triadic Structure Pattern Analysis**
   - What are the typical 3-node structure patterns in directed graphs?
   - Are there closed triads, transitive triples, or cyclic triples?
   - Typical algorithm: `triadic_census`

---

## II. Operator Capability Classification

| Capability Type               | Operator                                          | Description                                                                 |
| ----------------------------- | ------------------------------------------------- | --------------------------------------------------------------------------- |
| Node clustering coefficient   | `clustering`                                      | Compute local clustering coefficient for specified nodes or all nodes       |
| Average clustering coefficient| `average_clustering`                             | Compute average clustering coefficient for the whole graph or a node subset |
| Triangle count                | `triangles`                                       | Count number of triangles each node participates in                         |
| Global transitivity           | `transitivity`                                    | Compute the overall triangle closure ratio of the network                   |
| Square clustering             | `square_clustering`                              | Measure node’s tendency to participate in 4-cycles (redundant local structure) |
| Community - greedy modularity | `greedy_modularity_communities`                  | Clauset-Newman-Moore greedy merge to maximize modularity                  |
| Community - naive greedy modularity | `naive_greedy_modularity_communities`       | Naive greedy modularity optimization, suitable for teaching or small graphs |
| Community - divisive (GN)     | `girvan_newman`                                  | Iteratively remove critical edges to obtain hierarchical community structure |
| Community - synchronous LPA   | `label_propagation_communities`                  | Communities formed by majority label diffusion among neighbors              |
| Community - asynchronous LPA  | `asyn_lpa_communities`                           | Asynchronous label propagation supporting weights and random seeds          |
| Community - k-clique percolation | `k_clique_communities`                         | Overlapping communities based on k-clique percolation                       |
| Community - Louvain           | `louvain_communities`                            | Multi-level modularity optimization, fast community detection on large graphs |
| Community - Leiden            | `leiden_communities`                             | Improved version of Louvain, more stable and enforces community connectivity |
| Community - asynchronous fluid | `asyn_fluidc`                                   | Fluid-based diffusion to partition into a specified number of communities   |
| Graph bipartition             | `kernighan_lin_bisection`                        | Kernighan-Lin heuristic to partition graph into two parts                   |
| Bipartite graph spectral modularity bipartition | `spectral_modularity_bipartition` | Spectral method for modularity bipartition of bipartite graphs            |
| Cycle – simple cycles enumeration | `simple_cycles`                               | Enumerate all simple cycles in a graph                                      |
| Cycle – cycle basis           | `cycle_basis`                                    | Return a cycle basis for an undirected graph                                |
| Cycle – minimum cycle basis   | `minimum_cycle_basis`                            | Return a cycle basis with minimal total weight for an undirected graph      |
| Triadic census                | `triadic_census`                                 | Count the different 3-node structure patterns in a directed graph           |
| Quality – modularity          | `modularity`                                     | Compute modularity Q for a given community partition                        |
| Quality – coverage/performance| `partition_quality`                              | Compute coverage and performance for a community partition                  |
| Validity – is partition       | `is_partition`                                   | Check whether a list of communities forms a strict partition               |

---

## III. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph  
  - Clustering measures are mostly for undirected graphs, but some support directed or weighted forms.
  - Simple cycle enumeration is typically for directed graphs but can be used for some undirected scenarios.
  - Community detection is mostly for undirected graphs.
  - Bipartite algorithms require the input to conform to bipartite structure.

- **Common Outputs**
  - Metrics: `float`
  - Counts: `dict` / `int`
  - Community results: `list[set]` / `iterable[set]` / `iterator[tuple[set]]`
  - Cycle structures: `list[list[node]]` or iterator
  - Bisection result: `tuple[set, set]`
  - Validation: `bool`

> Notes:  
> - Community detection algorithm outputs are usually lists of node sets.  
> - `k_clique_communities` allows overlapping communities, so its result may not be a strict partition.  
> - Modularity, coverage, performance generally require a partition covering all nodes without overlap.  
> - Cycle enumeration and clique-based algorithms may produce exponential results; set limits or filter subgraphs for large graphs.

---

## IV. Detailed Operator Descriptions

## 4.1 Local Density & Clustering

### 1. clustering — Node Clustering Coefficient

**Description**  
Compute the local clustering coefficient for a node, measuring the proportion of connections among its neighbors – i.e., whether neighbors know each other.

**Product Value**
- Identify tightly-knit friend circles or gang cores
- Discover high-density relationship regions and key nodes
- Serve as structural features for fraud detection, social analysis, and recommendation systems

**Typical Scenarios**
- Social networks: find users with the tightest friend circles
- Transaction networks: identify accounts in closed transaction relationships
- Collaboration networks: discover members with high collaboration density
- Risk control networks: identify local gang structures

**Key Parameters**
- `nodes`: single node, list of nodes, or all nodes
- `weight`: edge weight representing relationship strength

**Applicability & Characteristics**
- Graph type: primarily undirected
- Output: `{node: score}` or single node score
- Complexity: typically related to square of node degree

---

### 2. average_clustering — Average Clustering Coefficient

**Description**  
Return the average clustering coefficient for the whole graph or a specified set of nodes. Values usually range between 0 and 1, summarizing the overall cliquishness of the network.

**Product Value**
- Provide a one-number summary of network clustering
- Suitable for comparisons across networks, time windows, or regions
- Monitor trends in network cliquishness or circle formation

**Typical Scenarios**
- Social network: overall clique density assessment
- Risk control: monitor gang formation trends
- City road networks: compare local redundancy levels
- Organizational collaboration: compare density of collaboration

**Key Parameters**
- `nodes`: only compute for a subgroup
- `weight`: incorporate relationship strength
- `count_zeros`: whether to include nodes with zero clustering coefficient in the average

**Applicability & Characteristics**
- Graph type: primarily undirected
- Output: `float`
- Complexity: typically related to square of node degree

---

### 3. transitivity — Global Transitivity

**Description**  
Compute the global transitivity of the network, i.e., the ratio of closed triads to all triads, measuring the overall triangle closure tendency.

**Product Value**
- Determine whether the network tends to be random, small-world, or cliquish
- Suitable for global structural characterization
- Support network modeling and comparison

**Typical Scenarios**
- Social networks: overall triangle closure degree
- Collaboration networks: tendency for three-way collaborations
- Risk control: closed structures in funds or accounts
- Knowledge graphs: transitive relationships among concepts

**Applicability & Characteristics**
- Graph type: primarily undirected
- Output: `float`
- Complexity: related to triangle counting

---

### 4. triangles — Triangle Count

**Description**  
Count the number of triangles each node participates in, or return the triangle count for specified nodes.

**Product Value**
- More triangles indicate a node lies in a tight circle
- Useful as a feature for gang identification, local density, and community core detection
- When combined with clustering coefficient, distinguishes “many connections” from “tight connections”

**Typical Scenarios**
- Social networks: users involved in many closed friend circles
- Transaction networks: accounts in closed transaction relationships
- Collaboration networks: group members with frequent three-way collaboration
- Recommendation networks: analysis of closed common-interest loops

**Key Parameters**
- `nodes`: single node, list, or all nodes

**Applicability & Characteristics**
- Graph type: undirected
- Output: `dict` or `int`
- Complexity: related to node degree and edge count

---

### 5. square_clustering — Square Clustering Coefficient

**Description**  
Measure the tendency of a node to participate in 4-cycles. A 4-cycle often represents local redundant relationships where two different paths connect the same targets.

**Product Value**
- Suitable for analyzing closed relationships in bipartite or near-bipartite structures
- Discover local alternative paths and structural redundancy
- More interpretable than triangles in user-item, author-paper, account-device networks

**Typical Scenarios**
- User-item network: multiple users purchasing multiple items
- Author-paper network: multiple authors co-authoring multiple papers
- Account-device network: multiple accounts sharing multiple devices
- Road network: local alternative route redundancy

**Key Parameters**
- `nodes`: compute only for a subset of nodes

**Applicability & Characteristics**
- Graph type: primarily undirected
- Output: `{node: score}`
- Complexity: related to local neighborhood size

---

## 4.2 Community Detection & Graph Partitioning

### 6. greedy_modularity_communities — Greedy Modularity Communities

**Description**  
Maximize modularity using a greedy merging strategy, outputting a list of communities. The algorithm starts from each node as its own community and repeatedly merges pairs that give the largest increase in modularity.

**Product Value**
- Classic, stable, and interpretable
- Default choice for mid-to-large scale undirected graphs
- Supports resolution parameter to control community size

**Typical Scenarios**
- Social networks: natural circle detection
- Transaction networks: initial gang identification
- Corporate relationship networks: group detection
- Knowledge networks: topic cluster identification

**Key Parameters**
- `weight`: edge weight
- `resolution`: community scale (larger yields smaller communities)
- `cutoff`: minimum number of communities
- `best_n`: maximum number of communities

**Applicability & Characteristics**
- Graph type: undirected
- Output: `list[set]`
- Suitable for: mid-to-large scale community detection

---

### 7. girvan_newman — Girvan-Newman Hierarchical Communities

**Description**  
Iteratively remove the most critical edges (by default, edges with highest betweenness centrality) to progressively split the graph, producing a hierarchical community structure from coarse to fine.

**Product Value**
- Highly interpretable, suitable for illustrating how communities are split
- Produces multi-level community partitions
- Good for small graphs or scenarios requiring an interpretable splitting path

**Typical Scenarios**
- Small-scale organizational relationship splitting
- Teaching complex network concepts
- Critical-edge-driven community fission analysis
- Risk control investigations needing an interpretable split path

**Key Parameters**
- `most_valuable_edge`: custom strategy to select the most valuable edge

**Applicability & Characteristics**
- Graph type: undirected
- Output: iterator over partitions
- Note: High complexity, not suitable for very large graphs

---

### 8. label_propagation_communities — Synchronous Label Propagation Communities

**Description**  
Form communities by diffusing labels to neighbors. Nodes iteratively adopt the majority label among neighbors; nodes with the same label form a community.

**Product Value**
- No need to specify number of communities
- Fast, suitable for coarse clustering on large graphs
- Can serve as a preprocessing step for more refined community detection

**Typical Scenarios**
- Large-scale social network rapid circle detection
- Recommendation system initial user interest grouping
- Communication network coarse partitioning
- Risk control candidate gang discovery

**Applicability & Characteristics**
- Graph type: undirected
- Output: `generator[set(node)]`
- Complexity: typically `O(V + E)` per iteration

---

### 9. asyn_lpa_communities — Asynchronous Label Propagation Communities

**Description**  
Asynchronous label propagation where node update order affects the result. Random seeds can improve reproducibility, and edge weights can influence label importance.

**Product Value**
- More flexible than synchronous LPA
- Supports weights and randomness control
- Fast community detection on large graphs

**Typical Scenarios**
- Weighted social network circle detection
- Large-scale transaction network preliminary gang identification
- Organizational communication network rapid grouping
- Recommendation network interest community partitioning

**Key Parameters**
- `weight`: edge weight influences label propagation
- `seed`: random seed for reproducibility

**Applicability & Characteristics**
- Graph type: undirected
- Output: `generator[set(node)]`
- Complexity: typically `O(V + E)` per iteration

---

### 10. k_clique_communities — k-Clique Percolation Communities

**Description**  
Use k-cliques as basic units; if two k-cliques share `k-1` nodes, they belong to the same community. Nodes can belong to multiple communities.

**Product Value**
- Ideal for detecting very dense core circles
- Supports overlapping communities, more realistic for social and collaboration networks
- Highly interpretable for gangs, collusion, and small group identification

**Typical Scenarios**
- Social networks: allowing one person to belong to multiple friend circles
- Collaboration networks: authors belonging to multiple research teams
- Risk control: accounts involved in multiple gang structures
- Biological networks: proteins participating in multiple functional modules

**Key Parameters**
- `k`: minimum clique size (larger k yields stricter communities)
- `cliques`: optional precomputed clique list to avoid recomputation

**Applicability & Characteristics**
- Graph type: undirected
- Output: `generator[frozenset(node)]`
- Note: Results can overlap, not necessarily a strict partition

---

### 11. louvain_communities — Louvain Communities

**Description**  
The classic multi-level modularity optimization method. It first performs local node moves to improve modularity, then compresses communities into super-nodes for further optimization.

**Product Value**
- Industry standard for large-scale community detection
- Fast and generally good performance
- Supports multi-level community structures and resolution control

**Typical Scenarios**
- Large-scale social network community detection
- Transaction network gang identification
- Communication network structural partitioning
- Knowledge graph topic community discovery

**Key Parameters**
- `weight`: edge weight
- `resolution`: community scale
- `threshold`: modularity improvement threshold
- `max_level`: maximum number of levels
- `seed`: random seed

**Applicability & Characteristics**
- Graph type: undirected
- Output: `list[set]`
- Characteristics: results may be affected by randomness; set seed for reproducibility

---

### 12. leiden_communities — Leiden Communities

**Description**  
Leiden is an improved version of Louvain, generally more stable and emphasizes community internal connectivity, avoiding partitions where a community is not well-connected internally.

**Product Value**
- Usually more robust community quality than Louvain
- Suitable for scenarios requiring strong internal connectivity
- Better for rigorous community analysis and production outputs

**Typical Scenarios**
- Large-scale social network precise grouping
- Financial risk control gang identification
- Biological network functional module identification
- Organizational network stable circle detection

**Key Parameters**
- `weight`: edge weight
- `resolution`: community scale
- `max_level`: maximum number of levels
- `seed`: random seed

**Applicability & Characteristics**
- Graph type: undirected
- Output: `list[set]`
- Characteristics: typically more stable than Louvain

---

### 13. naive_greedy_modularity_communities — Naive Greedy Modularity Communities

**Description**  
Optimize modularity using a naive greedy approach. Less efficient than the optimized version, but more intuitive. Suitable for teaching, small graph validation, or comparison.

**Product Value**
- Easy to explain algorithm logic
- Good for small-scale community detection validation
- Can serve as a reference or baseline against optimized greedy modularity

**Typical Scenarios**
- Small network community detection
- Teaching demonstrations of modularity maximization
- Comparing with optimized greedy modularity results
- Debugging community detection logic

**Key Parameters**
- `weight`: edge weight
- `resolution`: community scale

**Applicability & Characteristics**
- Graph type: undirected
- Output: list of community sets
- Note: Not recommended for very large graphs

---

### 14. spectral_modularity_bipartition — Spectral Modularity Bipartition

**Description**  
Use spectral methods to perform modularity-based bipartition on bipartite graphs, dividing nodes into two modules.

**Product Value**
- Suitable for community detection on bipartite graphs
- Preserves the original two-sided structure of user-item, author-paper, etc.
- Avoids information loss caused by projection onto one side

**Typical Scenarios**
- User-item network community detection
- Author-paper network research direction grouping
- Institution-project network module identification
- Account-device network risk cluster splitting

**Applicability & Characteristics**
- Graph type: bipartite
- Output: bipartition result (two sets)
- Suitable for: modularity analysis on bipartite graphs

---

### 15. asyn_fluidc — Asynchronous Fluid Communities

**Description**  
Community detection based on fluid diffusion. The user specifies the number of communities `k`, and the algorithm asynchronously updates to form the specified number of communities.

**Product Value**
- Explicit control over the number of communities
- Fast, suitable for scenarios requiring a fixed number of groups
- Like label propagation, good for rapid community partitioning

**Typical Scenarios**
- Networks needing fixed K partitions
- Recommendation systems with known number of user segments
- Operations network fixed zoning
- Risk control candidate gang generation with specified count

**Key Parameters**
- `k`: number of communities to partition into
- `max_iter`: maximum number of iterations
- `seed`: random seed

**Applicability & Characteristics**
- Graph type: typically requires a connected undirected graph
- Output: `iterable[set(node)]`
- Note: Requires advance specification of community count

---

### 16. kernighan_lin_bisection — Kernighan-Lin Bisection

**Description**  
Use the Kernighan-Lin heuristic to partition the graph into two parts, aiming to minimize the cut edge weight between them.

**Product Value**
- Suitable for graph bipartition, load balancing, and task splitting
- Can be used for two-region partition, AB-group experiments, etc.
- More oriented toward balanced cuts or reducing cross-group connections than generic community detection

**Typical Scenarios**
- Computational task graph bipartition scheduling
- Network partitioning and load balancing
- Structured splitting for A/B testing
- Circuit or module partitioning

**Key Parameters**
- `partition`: optional initial bipartition
- `max_iter`: maximum iterations
- `weight`: edge weight
- `seed`: random seed

**Applicability & Characteristics**
- Graph type: undirected
- Output: `tuple[set, set]`
- Characteristics: heuristic, not guaranteed to be globally optimal

---

## 4.3 Cycle Structures & Loop Detection

### 17. simple_cycles — Simple Cycle Enumeration

**Description**  
Enumerate all simple cycles in the graph. A simple cycle is a closed walk where no node (except start/end) repeats.

**Product Value**
- Discover fund recycling, circular transactions, cyclic dependencies
- Identify feedback loops and cyclic propagation structures
- Useful for closed-loop risk investigation in directed networks

**Typical Scenarios**
- Financial network: circular transaction chains
- Software dependency: cyclic dependency chains
- Biological network: feedback regulation loops
- Process network: abnormal closed-loop processes

**Key Parameters**
- `length_bound`: limit cycle length to avoid output explosion

**Applicability & Characteristics**
- Graph type: primarily directed
- Output: cycle path iterator or list
- Note: Number of cycles can be exponential; set length bound or filter subgraph for large graphs

---

### 18. cycle_basis — Cycle Basis

**Description**  
Return a cycle basis for an undirected graph – a set of independent cycles that can generate all cycles.

**Product Value**
- Decompose complex cycle structures into basic components
- Suitable for circuit, road, pipeline network cycle analysis
- Better for structural summarization than enumerating all cycles

**Typical Scenarios**
- Circuit networks: fundamental loop analysis
- Road networks: basic closed block identification
- Pipeline systems: basic loop structure identification
- Relationship networks: fundamental closed structure summary

**Key Parameters**
- `root`: optional starting node

**Applicability & Characteristics**
- Graph type: undirected
- Output: `list[list[node]]`

---

### 19. minimum_cycle_basis — Minimum Cycle Basis

**Description**  
Return a minimum cycle basis for an undirected graph – a set of independent cycles with minimal total length or cost.

**Product Value**
- Prefers lower cost, smaller, or better-weighted cycle bases compared to a generic basis
- Suitable for scenarios requiring the most compact closed-loop explanation
- Useful in circuits, roads, pipelines for low-cost cycle analysis

**Typical Scenarios**
- Circuit networks: minimal fundamental loop analysis
- Road networks: shortest closed block identification
- Pipeline systems: low-cost fundamental loop analysis
- Chemical structures: minimal ring system identification

**Key Parameters**
- `weight`: edge weight attribute (distance, cost, length)

**Applicability & Characteristics**
- Graph type: undirected
- Output: `list[list[node]]`
- Suitable for: small to medium graphs or scenarios needing minimal closed-loop summary

---

### 20. triadic_census — Triadic Census

**Description**  
Count the number of each type of 3-node subgraph (triad) in a directed graph. Different triad patterns represent reciprocity, transitivity, cyclicity, null connections, etc.

**Product Value**
- Characterize directed network structure at the triad level
- Determine whether the network favors transitive, cyclic, or reciprocal relationships
- Suitable for microstructure analysis of social, transaction, communication, organization, and ecological networks

**Typical Scenarios**
- Social networks: mutual following, one-way following, three-person closed loop statistics
- Transaction networks: three-account cyclic transaction structure analysis
- Organization networks: hierarchical transitive vs. feedback relationships
- Communication networks: three-node message flow pattern analysis

**Applicability & Characteristics**
- Graph type: directed
- Output: dictionary of triad type counts
- Suitable for: microstructure pattern statistics and network fingerprinting

---

## 4.4 Community Evaluation & Validation

### 21. modularity — Modularity Score

**Description**  
Compute the modularity Q value for a given community partition, measuring whether within-community edges are more abundant than random expectation and between-community edges relatively few.

**Product Value**
- Provides a unified quality score for community partitions
- Useful for comparing different community detection algorithms or parameter settings
- The objective function optimized by Louvain, Leiden, greedy modularity, etc.

**Typical Scenarios**
- Compare Louvain vs. Leiden partition quality
- Select better result after adjusting resolution parameter
- Evaluate whether a manual grouping fits the network structure
- Quality check before deploying community detection results

**Key Parameters**
- `communities`: list of communities (should be a strict partition normally)
- `weight`: edge weight
- `resolution`: resolution parameter

**Applicability & Characteristics**
- Output: `float`
- Note: Higher modularity is generally better, but must be interpreted in business context and community scale

---

### 22. partition_quality — Coverage and Performance

**Description**  
Compute two quality metrics for a community partition:

- **coverage**: proportion of edges that are inside communities
- **performance**: proportion of node pairs that are either inside the same community or not connected across communities

**Product Value**
- Evaluate partition quality from different angles
- Coverage emphasizes whether within-group edges are abundant
- Performance considers both within-group edges and between-group separation

**Typical Scenarios**
- Quality assessment of community detection results
- Comparison between manual grouping and algorithmic grouping
- Judging clarity of community boundaries
- Evaluating gang splitting effectiveness in risk control

**Applicability & Characteristics**
- Input: community partition
- Output: `(coverage, performance)`
- Note: Typically requires a strict partition

---

### 23. is_partition — Partition Validity Check

**Description**  
Check whether a given list of communities forms a strict partition, i.e., covers all nodes and no overlap between communities.

**Product Value**
- Basic validation before community evaluation
- Prevent missing or duplicate nodes that could distort metrics
- Quality check before passing community results downstream

**Typical Scenarios**
- Verify that algorithm output covers all users
- Check manual grouping for duplicate members
- Validate community results before computing modularity
- Legality check before writing community results to database

**Applicability & Characteristics**
- Input: graph and community list
- Output: `bool`
- Note: Overlapping results like `k_clique_communities` typically do not satisfy strict partition

---

## V. Recommended Usage Guide (Selection Advice)

- **First, measure whether the network is cliquish**
  - Global cliquishness: `average_clustering` / `transitivity`
  - Local cliquishness: `clustering`
  - Triangle closure: `triangles`
  - 4-cycle redundancy: `square_clustering`

- **Quickly obtain communities**
  - Default for large graphs: `louvain_communities` / `leiden_communities`
  - Fast coarse grouping: `label_propagation_communities` / `asyn_lpa_communities`
  - Fixed number of communities: `asyn_fluidc`

- **Require interpretability or hierarchical splits**
  - Hierarchical communities: `girvan_newman`
  - Bipartition: `kernighan_lin_bisection`
  - Small graph greedy validation: `naive_greedy_modularity_communities`

- **Find overlapping tight circles**
  - Overlapping dense communities: `k_clique_communities`

- **Handle bipartite graph communities**
  - Bipartite spectral modularity bipartition: `spectral_modularity_bipartition`

- **Detect closed loops, cyclic dependencies, or fund recycling**
  - All simple cycles: `simple_cycles`
  - Cycle basis: `cycle_basis`
  - Minimum cycle basis: `minimum_cycle_basis`

- **Analyze triadic patterns**
  - Directed triad statistics: `triadic_census`

- **Evaluate community results**
  - Modularity: `modularity`
  - Coverage and performance: `partition_quality`
  - Validity check: `is_partition`

---

## VI. Typical Questions That Can Be Answered Directly (Examples)

- "Is the network overall loose random or small-world? Give me an overall metric."
- "Which nodes have the tightest friend circles? Top-20 clustering coefficients."
- "Which nodes participate in the most triangles?"
- "Does this bipartite network have significant 4-cycle redundancy?"
- "Automatically partition the network into natural communities and output each community's members."
- "Use Louvain and Leiden to detect communities and compare modularity."
- "I want a fixed partition into 5 communities. How should I do it?"
- "Bisect the graph into two parts while minimizing cross-group connections."
- "Find overlapping core circles."
- "Is this community result a strict partition? Are any nodes missing or duplicated?"
- "I have two community partitions; which is better? Give modularity and coverage/performance."
- "Find all fund closed loops or cyclic dependency chains, with cycle length no more than 6."
- "Output the minimum cycle basis of the undirected graph for basic closed-loop structure analysis."
- "Count triadic patterns in the directed graph and see how many transitive and cyclic triads exist."

---

## VII. Engineering Implementation Considerations

1. **Clarify analysis objectives first**
   - Clustering coefficients answer "Is local relationship dense?"
   - Community detection answers "How do nodes naturally group?"
   - Cycle algorithms answer "Are there closed loops or cyclic paths?"
   - Evaluation metrics answer "Is this partition good?"

2. **No single correct answer for community detection**
   - Louvain, Leiden, LPA, Girvan-Newman have different mechanisms and may yield different results.
   - Combine `modularity`, `partition_quality`, and business interpretation for selection.
   - For stochastic algorithms, set `seed` for reproducibility.

3. **Prefer efficient algorithms for large graphs**
   - Large graphs: `louvain_communities`, `leiden_communities`, `asyn_lpa_communities`.
   - `girvan_newman` is more interpretable but suited for small graphs.
   - Clique and cycle enumeration may be very slow on large graphs.

4. **Distinguish overlapping vs. strict partitions**
   - `k_clique_communities` allows node to belong to multiple communities.
   - `modularity`, `partition_quality`, `is_partition` are typically for strict partitions.
   - Do not directly evaluate overlapping communities with strict-partition metrics unless transformed.

5. **Do not arbitrarily project bipartite graphs**
   - Projecting user-item, author-paper can create many spurious edges.
   - Prefer bipartite methods like `spectral_modularity_bipartition` that preserve the bipartite structure.
   - If projection is necessary, define the projection rule and edge weights clearly.

6. **Control output size for cycle algorithms**
   - `simple_cycles` can have exponentially many results.
   - Use `length_bound` or filter subgraph in production.
   - For undirected graphs, prefer `cycle_basis` or `minimum_cycle_basis` for structural summary.

7. **Triangles, squares, triads as structural features**
   - `triangles`, `square_clustering`, `triadic_census` can serve as ML or rule-based features.
   - They do not directly produce communities but capture local patterns and structural anomalies.

8. **Modularity has resolution limits**
   - `resolution` affects community size.
   - Higher resolution typically yields smaller communities, lower resolution larger communities.
   - Choose scale based on business scenario, not only the highest modularity.

---

## VIII. Operator List

| No. | Operator Name                          | Description (Chinese)                     |
|-----|----------------------------------------|-------------------------------------------|
| 1   | `clustering`                           | Node clustering coefficient               |
| 2   | `average_clustering`                   | Average clustering coefficient            |
| 3   | `transitivity`                         | Global transitivity                       |
| 4   | `triangles`                            | Triangle count                            |
| 5   | `square_clustering`                    | Square clustering coefficient             |
| 6   | `greedy_modularity_communities`        | Greedy modularity communities             |
| 7   | `girvan_newman`                        | Girvan-Newman hierarchical communities    |
| 8   | `label_propagation_communities`        | Synchronous label propagation communities |
| 9   | `asyn_lpa_communities`                 | Asynchronous label propagation communities|
| 10  | `k_clique_communities`                 | k-clique percolation overlapping communities |
| 11  | `louvain_communities`                  | Louvain communities                       |
| 12  | `leiden_communities`                   | Leiden communities                        |
| 13  | `simple_cycles`                        | Simple cycle enumeration                  |
| 14  | `cycle_basis`                          | Cycle basis                               |
| 15  | `modularity`                           | Modularity score                          |
| 16  | `partition_quality`                    | Coverage and performance                  |
| 17  | `is_partition`                         | Partition validity check                  |
| 18  | `naive_greedy_modularity_communities`  | Naive greedy modularity communities       |
| 19  | `spectral_modularity_bipartition`      | Spectral modularity bipartition (bipartite) |
| 20  | `minimum_cycle_basis`                  | Minimum cycle basis                       |
| 21  | `triadic_census`                       | Triadic census (directed)                 |
| 22  | `asyn_fluidc`                          | Asynchronous fluid communities            |
| 23  | `kernighan_lin_bisection`              | Kernighan-Lin bisection                   |