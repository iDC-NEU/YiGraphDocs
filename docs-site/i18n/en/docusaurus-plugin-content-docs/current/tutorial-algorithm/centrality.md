---
sidebar_position: 3
---

# Centrality and Criticality Operator Set

**Operator Category**: Centrality (Node/Edge Importance Measurement)

**Algorithm Count**: 21

**Applicable Stages**: Key node identification, influence assessment, bottleneck/bridge localization, network robustness analysis, propagation capability assessment, hierarchical structure analysis, bipartite graph importance ranking

**Product Positioning**: Provides a unified centrality capability base for answering “who is most important / who is the critical intermediary / which edge is most critical / how information can spread faster / which nodes affect network robustness / what hierarchical position a node occupies”.

---

## 1. Operator Set Overview

The Centrality operator set targets various relational networks, including social networks, transaction networks, citation networks, communication networks, transportation networks, dependency networks, biological networks, bipartite recommendation networks, and more. It provides systematic characterization capabilities for **nodes, edges, hierarchical positions, and propagation roles**, mainly covering the following problems:

1. **Connection-scale Influence**  
   - Who has the most connections?
   - Who is pointed to by the most nodes?
   - Who actively connects to the most nodes?
   - Typical algorithms: `degree_centrality`, `in_degree_centrality`, `out_degree_centrality`

2. **Distance-based Reachability Efficiency**  
   - Who is, on average, closer to other nodes?
   - Who still has strong reachability in disconnected networks?
   - Typical algorithms: `closeness_centrality`, `harmonic_centrality`

3. **Shortest-path Bridges and Intermediary Roles**  
   - Who appears most often on shortest paths?
   - Which edge is the most critical?
   - Which nodes carry the most network flow or path load?
   - Typical algorithms: `betweenness_centrality`, `edge_betweenness_centrality`, `load_centrality`

4. **Current Flow and Network Bottlenecks**  
   - If information flows like electrical current through the network, which nodes are most critical?
   - Which nodes have greater impact on overall connectivity and circulation capability?
   - Typical algorithm: `current_flow_betweenness_centrality`

5. **Global Authority and Spectral Centrality**  
   - Who is connected to other important nodes?
   - Who is more authoritative under random-walk or link-voting mechanisms?
   - Typical algorithms: `eigenvector_centrality`, `katz_centrality`, `pagerank`, `hits`

6. **Propagation, Percolation, and Diffusion Capability**  
   - Which nodes are suitable as diffusion seeds?
   - When some nodes are already activated or in specific states, who is more critical to propagation?
   - Typical algorithms: `voterank`, `percolation_centrality`

7. **Local Structure and Robustness Contribution**  
   - Who participates in more tightly knit substructures?
   - How much would deleting a node affect the overall network structure?
   - Typical algorithms: `subgraph_centrality`, `laplacian_centrality`

8. **Neighborhood Similarity and Local Centrality**  
   - Who is more important under common-neighbor relationships?
   - Which nodes have higher connection value in local neighborhoods?
   - Typical algorithm: `common_neighbor_centrality`

9. **Hierarchical Structure and Flow Position**  
   - Is a node upstream or downstream in the network?
   - Does the network have a clear trophic, control, or hierarchical structure?
   - Typical algorithm: `trophic_levels`

10. **Bipartite Graph Importance Ranking**  
   - In bipartite graphs such as user-item, author-paper, and institution-project, how can we simultaneously evaluate the importance of nodes on both sides?
   - Typical algorithm: `birank`

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operators | Function Description |
|---|---|---|
| Connection Scale (Unweighted) | `degree_centrality` | Measure direct node influence by the number of connections |
| Connection Scale (Directed) | `in_degree_centrality`, `out_degree_centrality` | Measure the influence of being pointed to and actively pointing, respectively |
| Distance Efficiency | `closeness_centrality`, `harmonic_centrality` | Characterize reachability efficiency based on distances to other nodes; harmonic centrality is more robust to unreachable nodes |
| Shortest-path Bridge (Node) | `betweenness_centrality`, `load_centrality` | Measure the extent to which a node acts as an intermediary, bridge, or path-load carrier |
| Shortest-path Bridge (Edge) | `edge_betweenness_centrality` | Measure the contribution of key connecting edges to global connectivity and shortest paths |
| Current-flow Centrality | `current_flow_betweenness_centrality` | Measure node criticality in network circulation based on current-flow models |
| Global Authority / Influence (Spectral / Iterative) | `eigenvector_centrality`, `katz_centrality`, `pagerank`, `hits` | Measure global influence from perspectives such as “being connected to important nodes makes you important,” random walks, and Hub-Authority roles |
| Clique Structure Contribution (Spectral) | `subgraph_centrality` | Measure the extent to which a node participates in cycles, small cliques, and substructures |
| Diffusion Seed Selection | `voterank` | Select dispersed seeds with strong coverage through voting and suppression mechanisms |
| Percolation Propagation Centrality | `percolation_centrality` | Measure node criticality in percolation and diffusion when node states participate in propagation |
| Second-order Centrality | `second_order_centrality` | Evaluate node stability and centrality based on return-time fluctuations in random walks |
| Laplacian Centrality | `laplacian_centrality` | Measure node contribution to graph energy, structural robustness, and connectivity |
| Common-neighbor Centrality | `common_neighbor_centrality` | Measure the importance of nodes or local links based on common-neighbor structure |
| Hierarchy / Trophic Analysis | `trophic_levels` | Compute hierarchical positions or flow levels of nodes in directed networks |
| Bipartite Ranking | `birank` | Simultaneously evaluate the importance and mutual reinforcement of two node types in bipartite graphs |

---

## 3. General Input and Output Conventions

- **Input `G`**: NetworkX graph object  
  - Can be an undirected graph `Graph`
  - Can be a directed graph `DiGraph`
  - Some algorithms support weighted graphs
  - Some algorithms are designed for bipartite graphs or connected graphs

- **Common Outputs**
  - Node centrality: `{node: score}` dictionary
  - Edge centrality: `{(u, v): score}` dictionary
  - HITS: `(hubs_dict, authorities_dict)`
  - VoteRank: node list ranked by influence
  - BiRank: importance scores for nodes on both sides of a bipartite graph
  - Hierarchical results: `{node: level}` or node hierarchy score dictionary

> Note: Different centrality algorithms interpret weights differently.  
> Shortest-path-based centralities usually interpret weights as **distance / cost / resistance**; spectral centrality, PageRank, HITS, BiRank, and similar methods usually interpret weights as **connection strength / transition probability / voting weight**. In engineering practice, the semantic meaning of weight fields should be made explicit.

---

## 4. Detailed Operator Descriptions

### 1. degree_centrality —— Degree Centrality (Connection Scale)

**Function Description**  
Compute the degree centrality of each node: the normalized result of the node’s number of connections at the scale of the whole network, used to measure the breadth of direct relationship coverage.

**Product Value**
- Intuitive and fast, suitable for rapid screening on large graphs
- Identifies highly connected accounts, highly interactive users, and highly dependent components
- Can serve as a baseline ranking indicator before more complex centrality algorithms

**Typical Scenarios**
- Social networks: find users with the most friends or interactions
- Transaction networks: find accounts with the most counterparties
- Dependency networks: find service modules with the most dependencies or dependents

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Usually not used
- Output: `{node: score}`
- Complexity: `O(V + E)`

---

### 2. in_degree_centrality —— In-degree Centrality (Attention / Being Called)

**Function Description**  
In directed graphs, compute the extent to which each node is pointed to, reflecting its connection-level influence in terms of being cited, followed, or called.

**Product Value**
- Suitable for characterizing passive influence
- Can identify core nodes depended on, cited, or followed by many others
- Combined with out-degree centrality, it helps distinguish authoritative nodes from active nodes

**Typical Scenarios**
- Citation networks: most-cited papers
- Follower networks: accounts with the most followers
- Call dependencies: core services with the highest call frequency

**Applicability and Characteristics**
- Graph Type: Directed graph
- Weight: Usually not used
- Output: `{node: score}`
- Complexity: `O(V + E)`

---

### 3. out_degree_centrality —— Out-degree Centrality (Active Diffusion / Active Calling)

**Function Description**  
In directed graphs, compute the extent to which each node actively points to other nodes, reflecting the activity of active linking, active propagation, and active invocation.

**Product Value**
- Identifies active spreaders, broadcasters, or invocation entry points
- Complements in-degree centrality and helps distinguish popularity from activeness
- Suitable for analyzing outward-facing nodes and upstream initiating nodes

**Typical Scenarios**
- Social networks: active accounts following many others
- Citation networks: survey papers citing many references
- Dependency networks: entry services calling many downstream modules

**Applicability and Characteristics**
- Graph Type: Directed graph
- Weight: Usually not used
- Output: `{node: score}`
- Complexity: `O(V + E)`

---

### 4. closeness_centrality —— Closeness Centrality (Shortest Average Distance)

**Function Description**  
Compute the reciprocal of the sum of shortest-path distances from a node to other nodes. The shorter the distances, the closer the node is to the network center and the higher its reachability efficiency.

**Product Value**
- Identifies nodes that can reach the whole network fastest
- Suitable for information distribution, resource scheduling, and network efficiency analysis
- Can combine edge distances representing road mileage, communication latency, or business cost

**Typical Scenarios**
- Transportation networks: hub stations with shortest average arrival times
- Communication networks: relay nodes with minimum average hops or latency
- Organizational networks: team members who can reach most people fastest

**Key Parameters**
- `u`: compute only a single node
- `distance`: edge distance attribute
- `wf_improved`: whether to scale by reachable proportion, useful for disconnected graphs

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported, usually interpreted as distance
- Output: `{node: score}` or single-node score
- Complexity: Usually `O(V * (V + E))`

---

### 5. betweenness_centrality —— Betweenness Centrality (Critical Intermediary / Bridge)

**Function Description**  
Measure the extent to which a node lies on shortest paths between other pairs of nodes. The more shortest paths pass through the node, the more it acts like a bridge, gateway, or intermediary in the network.

**Product Value**
- Locates structural holes and cross-community connectors
- Identifies single-point failure risks
- Suitable for network robustness, bottleneck analysis, and critical node identification

**Typical Scenarios**
- Social networks: intermediaries across social circles
- Transaction networks: key transitional accounts in fund chains
- Transportation networks: critical hubs or unavoidable intersections

**Key Parameters**
- `k`: sampling approximation, suitable for large graphs
- `weight`: edge weight attribute, usually interpreted as distance
- `normalized`: whether to normalize
- `endpoints`: whether to include endpoints in shortest-path counting
- `seed`: random seed for sampling

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported, usually interpreted as distance
- Output: `{node: score}`
- Complexity: Exact computation is usually expensive; sampling approximation is recommended for large graphs

---

### 6. edge_betweenness_centrality —— Edge Betweenness Centrality (Critical Connecting Edge)

**Function Description**  
Measure how many shortest paths between node pairs pass through an edge. Edges with high edge betweenness are often inter-community links, bridge edges, or critical links.

**Product Value**
- Identifies critical edges whose removal causes the greatest impact
- Supports link reinforcement, community partitioning, and network vulnerability analysis
- Can be used to locate bottleneck connections

**Typical Scenarios**
- Transportation networks: critical road segments, bridges, tunnels
- Communication networks: critical links, optical fiber segments
- Supply chain networks: key cross-region supply connections

**Key Parameters**
- `k`: sampling approximation
- `weight`: edge weight attribute, usually interpreted as distance
- `normalized`: whether to normalize
- `seed`: sampling random seed

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported
- Output: `{(u, v): score}`
- Complexity: Exact computation is usually expensive; approximation is recommended for large graphs

---

### 7. eigenvector_centrality —— Eigenvector Centrality (Connected to Important Nodes)

**Function Description**  
Score nodes based on the principal eigenvector of the adjacency matrix. A node is important not only because it has many connections, but also because it is connected to important nodes.

**Product Value**
- Emphasizes high-quality connections more than degree centrality
- Suitable for discovering core nodes within core circles
- Captures reinforcement structures where important nodes support each other

**Typical Scenarios**
- Social networks: key influencers in core communities
- Citation networks: papers connected to high-impact nodes
- Organizational networks: members working closely with critical positions

**Key Parameters**
- `max_iter`: maximum number of iterations
- `tol`: convergence tolerance
- `nstart`: initial vector
- `weight`: connection strength weight

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported, usually interpreted as connection strength
- Output: `{node: score}`
- Complexity: `O(k * (V + E))`, where `k` is the number of iterations

---

### 8. katz_centrality —— Katz Centrality (Accumulated Multi-hop Influence with Decay)

**Function Description**  
Based on eigenvector centrality, consider contributions from paths of all lengths, and control the influence of long-distance connections through a decay factor.

**Product Value**
- Considers both direct and indirect influence
- Suitable for business scenarios where influence decays along chains
- Can assign scores to nodes with zero in-degree or few connections via a baseline term

**Typical Scenarios**
- Risk control propagation: risk decays along transaction chains
- Citation networks: multi-hop citation influence analysis
- Organizational networks: cross-level collaboration influence assessment

**Key Parameters**
- `alpha`: decay coefficient
- `beta`: baseline influence
- `max_iter`: maximum number of iterations
- `tol`: convergence tolerance
- `normalized`: whether to normalize
- `weight`: connection strength weight

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported, usually interpreted as connection strength
- Output: `{node: score}`
- Complexity: `O(k * (V + E))`

---

### 9. pagerank —— PageRank (Global Importance via Random Walk)

**Function Description**  
Treat the graph as a random-walk process. A node’s importance accumulates from incoming links from other important nodes, while random jumps are controlled by a damping factor.

**Product Value**
- Suitable for stable importance ranking in large-scale networks
- Personalized vectors can support ranking for specific topics or groups
- Highly interpretable for web, citation, recommendation, transaction, and other link-based networks

**Typical Scenarios**
- Link networks: authority ranking of pages or resources
- Citation networks: ranking papers or patents by influence
- Email/communication networks: prioritizing key contacts
- Transaction networks: identifying important accounts

**Key Parameters**
- `alpha`: damping factor
- `personalization`: personalization vector
- `dangling`: dangling-node handling strategy
- `max_iter`: maximum number of iterations
- `tol`: convergence tolerance
- `weight`: transition weight

**Applicability and Characteristics**
- Graph Type: Mainly directed graphs; undirected graphs can be treated as bidirectional directed graphs
- Weight: Supported, usually interpreted as transition weight
- Output: `{node: score}`
- Complexity: `O(k * (V + E))`

---

### 10. hits —— HITS (Dual Roles of Hub / Authority)

**Function Description**  
Compute two scores for each node:

- **Hub**: the extent to which the node points to high-authority nodes
- **Authority**: the extent to which the node is pointed to by high-hub nodes

**Product Value**
- Distinguishes resource-entry roles from content-authority roles
- Suitable for highly directional networks such as citation, linking, and dependency graphs
- More suitable than a single centrality measure for explaining dual-role structures

**Typical Scenarios**
- Website links: identifying navigation sites and content sites
- Citation networks: identifying survey papers and authoritative papers
- Dependency networks: identifying call entry points and core modules

**Key Parameters**
- `max_iter`: maximum number of iterations
- `tol`: convergence tolerance
- `nstart`: initial vector
- `normalized`: whether to normalize

**Applicability and Characteristics**
- Graph Type: Mainly directed graphs
- Weight: Usually not used
- Output: `(hubs_dict, authorities_dict)`
- Complexity: `O(k * (V + E))`

---

### 11. harmonic_centrality —— Harmonic Centrality (More Robust to Unreachability)

**Function Description**  
Sum the reciprocals of distances to other nodes. Unreachable nodes contribute 0, making it more stable than closeness centrality in disconnected graphs.

**Product Value**
- Provides usable ranking even in disconnected networks
- Balances global reachability and local access efficiency
- Suitable for graphs with islands, weak connectivity, or broken structure

**Typical Scenarios**
- Knowledge graphs: identifying highly reachable nodes across multiple communities
- Citation networks: analyzing cross-domain reachability
- Transportation/communication networks: evaluating critical nodes under failures or disconnections

**Key Parameters**
- `nbunch`: compute only a subset of nodes
- `sources`: specify a source set
- `distance`: edge distance attribute

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported, usually interpreted as distance
- Output: `{node: score}`
- Complexity: Usually `O(V * (V + E))`

---

### 12. load_centrality —— Load Centrality (Traffic-bearing Intermediary)

**Function Description**  
Measure how much load a node carries under shortest-path traffic splitting. Similar to betweenness centrality, but emphasizes interpretation in terms of flow load through the network.

**Product Value**
- Suitable for analyzing traffic load and relay pressure
- Can be used in transportation, communication, and system dependency networks
- Supports identifying nodes likely to become bottlenecks

**Typical Scenarios**
- Communication networks: nodes carrying the most relay load
- Transportation networks: hubs carrying the most commuting flow
- System dependencies: gateway modules carrying the most forwarding or aggregation

**Key Parameters**
- `cutoff`: consider only paths within a certain length
- `weight`: edge weight attribute
- `normalized`: whether to normalize

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported, usually interpreted as distance
- Output: `{node: score}`
- Complexity: Usually expensive; for large graphs, combine with cutoff or approximation strategies

---

### 13. subgraph_centrality —— Subgraph Centrality (Participation in Tight Substructures)

**Function Description**  
Use spectral methods to measure the extent to which a node participates in cycles, small cliques, and substructures. The more tight structures a node participates in, the higher its subgraph centrality.

**Product Value**
- Identifies core nodes in tightly connected groups
- Sensitive to collaboration, gang, and cyclic structures
- Suitable for deep structural analysis in small to medium-sized networks

**Typical Scenarios**
- Social networks: core people in tightly knit friend circles
- Biological networks: genes or proteins participating in key functional modules
- Collaboration networks: authors or teams participating in dense collaboration groups

**Applicability and Characteristics**
- Graph Type: Usually undirected graphs
- Weight: Usually not used
- Output: `{node: score}`
- Complexity: Related to spectral decomposition; usually better for small to medium-sized graphs

---

### 14. voterank —— VoteRank (Diffusion Seed Selection)

**Function Description**  
Through a mechanism of “node voting + suppressing neighbor voting ability after selection,” iteratively choose a set of influential seed nodes so that the seeds are more dispersed and have broader coverage.

**Product Value**
- Less likely to cluster than simply choosing highest-degree nodes
- Suitable for influence maximization, marketing reach, and monitoring point selection
- Can output a set of candidate nodes with strong coverage capability

**Typical Scenarios**
- Marketing propagation: choose more dispersed seed users
- Security alerts: choose critical nodes for priority monitoring
- Caching/content distribution: choose representative nodes for content delivery

**Key Parameters**
- `number_of_nodes`: number of seed nodes to return

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Usually not used
- Output: node list
- Complexity: Usually near linear, depending on the number of selected seeds

---

### 15. current_flow_betweenness_centrality —— Current-flow Betweenness Centrality

**Function Description**  
Compute node centrality based on an electrical current network model. Unlike ordinary betweenness centrality, which only focuses on shortest paths, current-flow betweenness considers flow contributions across all possible paths.

**Product Value**
- Better suited to characterizing intermediary roles under real multi-path diversion
- Can identify nodes that play a critical role in the overall circulation structure
- More interpretable for network robustness and redundant-path analysis

**Typical Scenarios**
- Power networks: identifying key transmission nodes
- Communication networks: key relays under multi-path transmission
- Transportation networks: bottleneck analysis when multiple alternative routes exist
- Biological networks: key relay nodes in signal pathways

**Key Parameters**
- `weight`: edge weight attribute
- `normalized`: whether to normalize
- `solver`: linear-system solving method
- `dtype`: numeric precision type

**Applicability and Characteristics**
- Graph Type: Usually connected undirected graphs
- Weight: Supported
- Output: `{node: score}`
- Complexity: Involves linear algebra solving; usually suitable for small to medium-sized graphs or sparse-optimization scenarios

---

### 16. percolation_centrality —— Percolation Centrality

**Function Description**  
When nodes have state values or activation levels, measure the extent to which a node is critical in network percolation, propagation, or diffusion processes.

**Product Value**
- Incorporates node states into centrality computation
- Suitable for dynamic propagation, risk diffusion, epidemic spreading, and similar scenarios
- Can identify the most critical intermediary nodes under the current propagation state

**Typical Scenarios**
- Risk propagation: influence analysis of infected or high-risk accounts
- Epidemic networks: key spreaders in disease diffusion
- Public opinion networks: key users in hotspot information diffusion
- Fault propagation: transmission analysis of abnormal states in systems

**Key Parameters**
- `states`: node states or percolation states
- `weight`: edge weight attribute
- `attribute`: node-state attribute field

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Weight: Supported
- Output: `{node: score}`
- Suitable for analysis combined with time slices, state values, or propagation stages

---

### 17. second_order_centrality —— Second-order Centrality

**Function Description**  
Measure node centrality based on the standard deviation of random-walk return times. The more stably a node is returned to or visited, the more structurally central it usually is.

**Product Value**
- Measures node importance from the perspective of random-walk stability
- Can complement standard metrics such as PageRank and closeness centrality
- Suitable for analyzing stable centers and structurally balanced positions in networks

**Typical Scenarios**
- Social networks: identifying users in stable core positions
- Communication networks: stably reachable relay nodes
- Recommendation networks: objects more stably reached under random walks
- Complex networks: evaluating node access stability

**Applicability and Characteristics**
- Graph Type: Usually connected undirected graphs
- Weight: Depends on implementation support
- Output: `{node: score}`
- Note: Computation usually involves random-walk-related matrices and can be expensive at larger scales

---

### 18. laplacian_centrality —— Laplacian Centrality

**Function Description**  
Evaluate the impact of a node on the overall network structure, connectivity, and robustness by measuring its contribution to graph Laplacian energy.

**Product Value**
- Emphasizes the effect of node deletion or change on the overall network structure
- Suitable for network robustness and vulnerability analysis
- Can identify important nodes for maintaining graph structural integrity

**Typical Scenarios**
- Infrastructure networks: identifying critical facility nodes
- Communication networks: nodes affecting overall connectivity quality
- Supply chain networks: companies or warehouses affecting system stability
- Risk-control networks: critical accounts whose removal damages structural stability

**Key Parameters**
- `weight`: edge weight attribute
- `normalized`: whether to normalize

**Applicability and Characteristics**
- Graph Type: Usually undirected graphs
- Weight: Supported
- Output: `{node: score}`
- Suitable for network robustness, vulnerability, and node-removal impact analysis

---

### 19. common_neighbor_centrality —— Common-neighbor Centrality

**Function Description**  
Measure the importance of nodes or local links based on common-neighbor structure. The more common neighbors there are, the more likely the nodes lie in a tighter local relationship structure.

**Product Value**
- Suitable for characterizing connection value in local structures
- Useful for similarity, potential relationship, and local influence analysis
- Can assist link prediction, community analysis, and local core identification

**Typical Scenarios**
- Social networks: potentially key users with many mutual friends
- Recommendation systems: local user-interest relationship analysis
- Collaboration networks: common collaborator structure analysis
- Risk-control networks: identifying common counterparties or common related persons

**Applicability and Characteristics**
- Graph Type: Usually undirected graphs
- Weight: Usually not used
- Output: centrality scores or candidate relationship scores
- Suitable for local structure analysis and neighborhood similarity analysis

---

### 20. trophic_levels —— Trophic Levels / Hierarchical Position

**Function Description**  
Compute hierarchical positions of nodes in directed networks. This algorithm is often used in networks with directional flow relationships to characterize whether nodes are upstream, midstream, or downstream.

**Product Value**
- Identifies hierarchical structure in networks
- Suitable for graphs with clear flow direction, dependency direction, or control direction
- Can be used to determine whether a system has clear upstream-downstream relationships

**Typical Scenarios**
- Ecological networks: trophic-level analysis of species
- Supply chain networks: hierarchy of upstream suppliers and downstream customers
- Dependency networks: identifying low-level modules, intermediate modules, and upper-level applications
- Fund-flow networks: analyzing fund source, relay, and terminal positions

**Applicability and Characteristics**
- Graph Type: Directed graph
- Weight: Can be combined with edge strength or flow
- Output: `{node: level}`
- Suitable for hierarchical structure, directional flow, and upstream-downstream position analysis

---

### 21. birank —— BiRank Bipartite Ranking

**Function Description**  
BiRank is a ranking algorithm for bipartite graphs. Through mutual reinforcement between the two sides, it simultaneously computes importance scores for both types of nodes.

**Product Value**
- Suitable for bipartite relation networks such as user-item, author-paper, and institution-project
- Can simultaneously evaluate the importance of nodes on both sides
- Useful for recommendation, ranking, resource prioritization, and influence assessment

**Typical Scenarios**
- Recommendation systems: two-sided importance ranking of users and items
- Academic networks: joint evaluation of author and paper influence
- Recruitment networks: importance of candidate-position matching
- Enterprise networks: analysis of institution-project and supplier-product relations

**Key Parameters**
- Two-side node sets of the bipartite graph
- Edge weights: interaction strength, rating, count, or association strength
- Iterative convergence parameters: maximum iterations, convergence threshold, etc.

**Applicability and Characteristics**
- Graph Type: Bipartite graph
- Weight: Supported, usually interpreted as interaction strength
- Output: score dictionaries for both sides
- Complexity: Usually iterative computation, approximately `O(k * E)`

---

## 5. Recommended Usage Guide (Practical Suggestions)

- **Quickly find large accounts / active nodes**: `degree_centrality` / `in_degree_centrality` / `out_degree_centrality`
- **Find bridges and structural holes**: `betweenness_centrality` / `edge_betweenness_centrality`
- **Find traffic-bearing nodes**: `load_centrality`
- **Find key nodes under multi-path flow**: `current_flow_betweenness_centrality`
- **Find nodes close to everyone**: `closeness_centrality`
- **Evaluate reachability in disconnected graphs**: `harmonic_centrality`
- **Find authorities and core circles**: `pagerank` / `eigenvector_centrality` / `katz_centrality`
- **Distinguish entry points from authoritative content**: `hits`
- **Find tight-group cores**: `subgraph_centrality`
- **Select diffusion seeds**: `voterank`
- **Identify critical nodes with known propagation states**: `percolation_centrality`
- **Evaluate node contribution to network robustness**: `laplacian_centrality`
- **Analyze local common-neighbor structure**: `common_neighbor_centrality`
- **Analyze hierarchical positions in directed networks**: `trophic_levels`
- **Two-sided ranking in bipartite graphs**: `birank`

---

## 6. Typical Directly Answerable Questions (Examples)

- “Output the Top-20 nodes with the highest degree centrality.”
- “Which nodes are pointed to by the most other nodes?”
- “Which nodes actively connect to the most downstream nodes?”
- “Output the Top-20 nodes with the highest betweenness centrality to identify key intermediaries.”
- “Compute edge betweenness centrality for all edges and output the Top-50 critical edges in descending order.”
- “Which nodes are most critical under a multi-path flow model?”
- “Which nodes carry the most load in the current network?”
- “Who has the highest PageRank in the current email communication network?”
- “What are the Top-20 users by harmonic centrality in a disconnected social graph?”
- “Use VoteRank to select 30 diffusion seed nodes for marketing reach.”
- “When propagation states are known, which nodes have the highest percolation centrality?”
- “Which nodes would have the greatest impact on the overall network structure if removed?”
- “In this directed network, which nodes are upstream and which are downstream?”
- “In a user-item bipartite graph, give importance rankings for both users and items.”

---

## 7. Engineering Implementation Notes

1. **Clarify the meaning of centrality first**
   - Degree centrality emphasizes the number of direct connections.
   - Closeness centrality emphasizes reachability efficiency.
   - Betweenness centrality emphasizes bridge and intermediary roles.
   - PageRank, eigenvector centrality, and Katz emphasize global authority and reinforcement among important nodes.
   - Laplacian centrality emphasizes node contribution to overall structural stability.

2. **Unify weight semantics**
   - In shortest-path-based centralities, weight usually represents distance, cost, or resistance; smaller means closer.
   - In spectral and random-walk algorithms, weight usually represents connection strength, interaction frequency, or transition weight; larger means stronger.
   - If a graph has multiple weight fields, clearly define which field each algorithm uses.

3. **Be careful with disconnected graphs**
   - Closeness centrality may be affected by unreachable nodes in disconnected graphs.
   - In disconnected graphs, prefer `harmonic_centrality`.
   - Current-flow centralities usually require connected graphs; check connectivity before use.

4. **Pay attention to performance on large graphs**
   - Exact betweenness centrality and edge betweenness centrality are computationally expensive.
   - Use sampling, Top-K strategies, or offline batch processing when needed.
   - Spectral-decomposition and current-flow algorithms are more suitable for small to medium-sized graphs or sparse-optimization scenarios.

5. **Propagation metrics should be combined with business state**
   - `voterank` is suitable for state-free diffusion seed selection.
   - `percolation_centrality` is suitable for propagation analysis when node states are known.
   - Different propagation stages may produce different critical node rankings.

6. **Do not directly apply ordinary centrality to bipartite graphs**
   - For bipartite graphs such as user-item, author-paper, and institution-project, prefer `birank`.
   - BiRank can simultaneously characterize the mutual reinforcement of nodes on both sides.
   - If directly projected into a one-side graph, original bipartite relation information may be lost.

7. **Interpret rankings together with business thresholds**
   - High centrality does not necessarily mean “most important in business terms.”
   - It is recommended to combine node attributes, edge weights, time windows, community structure, and anomaly rules for comprehensive judgment.

---

## 8. Operator Checklist

| No. | Operator Name | Description |
|---:|---|---|
| 1 | `degree_centrality` | Degree centrality |
| 2 | `in_degree_centrality` | In-degree centrality |
| 3 | `out_degree_centrality` | Out-degree centrality |
| 4 | `closeness_centrality` | Closeness centrality |
| 5 | `betweenness_centrality` | Betweenness centrality |
| 6 | `edge_betweenness_centrality` | Edge betweenness centrality |
| 7 | `eigenvector_centrality` | Eigenvector centrality |
| 8 | `katz_centrality` | Katz centrality |
| 9 | `pagerank` | PageRank centrality |
| 10 | `hits` | HITS Hub/Authority centrality |
| 11 | `harmonic_centrality` | Harmonic centrality |
| 12 | `load_centrality` | Load centrality |
| 13 | `subgraph_centrality` | Subgraph centrality |
| 14 | `voterank` | VoteRank diffusion seed selection |
| 15 | `current_flow_betweenness_centrality` | Current-flow betweenness centrality |
| 16 | `percolation_centrality` | Percolation centrality |
| 17 | `second_order_centrality` | Second-order centrality |
| 18 | `laplacian_centrality` | Laplacian centrality |
| 19 | `common_neighbor_centrality` | Common-neighbor centrality |
| 20 | `trophic_levels` | Trophic levels / hierarchical position |
| 21 | `birank` | BiRank bipartite ranking |
