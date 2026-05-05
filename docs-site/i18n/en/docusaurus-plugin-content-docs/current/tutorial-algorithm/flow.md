---
sidebar_position: 8
---
# Flow & Cut Operators

**Operator Category**: Flow & Cut (network flow, cut structure, boundary analysis)

**Number of Algorithms**: 7

**Applicable Stages**: network capacity analysis, bottleneck identification, resource scheduling, minimum cut evaluation, global resilience analysis, community boundary characterization, node/edge expansion capability assessment

**Product Positioning**: Provide a unified flow and cut capability base for answering questions such as: "What is the maximum flow?", "Where are the bottlenecks?", "What is the minimum cost to disconnect the network?", "What is the structure of the minimum cut between any two nodes?", "How tightly is a node set connected to the outside?", "Is the community boundary clear?"

---

## I. Operator Set Overview

The Flow & Cut operator set addresses four core categories:

1. **Maximum Flow**
   - From source to sink, what is the maximum flow that can be sent?
   - Which edges or nodes become bottlenecks first?
   - Typical algorithms: `edmonds_karp`, `shortest_augmenting_path`, `preflow_push`

2. **Capacity Scaling & Minimum Cost Flow**
   - How to optimally allocate flow under capacity, supply/demand, and cost constraints?
   - How to achieve cost-optimal scheduling in multi-source multi-sink networks?
   - Typical algorithm: `capacity_scaling`

3. **Global Minimum Cut Structure**
   - What is the minimum cut between any two nodes?
   - Can all-pairs minimum cuts be compressed into a single tree?
   - Typical algorithm: `gomory_hu_tree`

4. **Boundary & Expansion Analysis**
   - How tightly is a node set connected to the outside?
   - Are the boundaries of a community, gang, or subgraph clear?
   - What is the connection strength of a node set expanding outward?
   - Typical algorithms: `edge_expansion`, `node_boundary`

---

## II. Operator Capability Classification

| Capability Type               | Operator                                          | Description                                                                 |
| ----------------------------- | ------------------------------------------------- | --------------------------------------------------------------------------- |
| Classical maximum flow        | `edmonds_karp`                                    | BFS-based augmenting path max flow, interpretable, suitable for small/medium networks |
| Shortest augmenting path max flow | `shortest_augmenting_path`                    | Uses distance labels and shortest augmenting paths for efficiency           |
| Push-relabel maximum flow     | `preflow_push`                                    | Push/Relabel operations for max flow, suitable for dense/complex networks   |
| Capacity scaling min-cost flow| `capacity_scaling`                                | Handles capacity, supply/demand, and cost constraints for min-cost flow     |
| All-pairs min cut structure   | `gomory_hu_tree`                                  | Compresses all-pairs min cut values into a cut tree for undirected graphs   |
| Edge expansion                | `edge_expansion`                                  | Measures boundary connection strength of a node set                         |
| Node boundary                 | `node_boundary`                                   | Returns the set of external nodes adjacent to a given node set              |

---

## III. General Input/Output Conventions

### 3.1 Input

- **G**: NetworkX graph object  
  - Max flow algorithms are typically for directed graphs, but some support undirected via conversion.
  - `gomory_hu_tree` is typically for undirected graphs.
  - `edge_expansion` / `node_boundary` can be used on undirected or directed graphs for set boundary analysis.

- **capacity**: edge capacity attribute name, commonly defaulting to `"capacity"`

- **s / t**: source and sink nodes for max flow, min cut, or source-sink network analysis

- **demand**: node demand attribute for min-cost flow problems  
  - Supply nodes usually have negative demand.
  - Consumption nodes usually have positive demand.

- **weight**: edge unit flow cost or weight attribute for min-cost flow, expansion, or boundary computations

- **nbunch / S**: node set for boundary and expansion analysis

### 3.2 Output

- **Max flow algorithms**: residual network; flow value, flow distribution, and saturated edges can be derived.
- **Capacity scaling / min-cost flow**: optimal flow assignment and total cost.
- **Gomory-Hu Tree**: an undirected tree where edge weights encode the min cut values between node pairs in the original graph.
- **Edge expansion**: a numeric metric.
- **Node boundary**: a set of boundary nodes.

---

## IV. Detailed Operator Descriptions

### 1. edmonds_karp — Classical Maximum Flow (Edmonds‑Karp)

**Description**  
Based on the Ford‑Fulkerson method, this algorithm uses BFS to find the shortest augmenting path from source `s` to sink `t` at each iteration, increases flow along that path, and stops when no augmenting path exists.

**Product Value**
- Highly interpretable among max flow algorithms.
- Suitable for small‑ to medium‑scale networks, teaching, and debugging.
- Outputs a residual network for analyzing bottleneck edges, residual capacity, and flow distribution.

**Typical Scenarios**
- Logistics: maximum cargo transport from warehouse to destination.
- Communication: maximum bandwidth from source to target node.
- Pipeline systems: maximum delivery capacity from supply to demand.
- Task scheduling: maximum matching of resources from supply to demand.

**Key Parameters**
- `s`: source node
- `t`: sink node
- `capacity`: edge capacity attribute
- `cutoff`: stop early after reaching a specified flow value

**Applicability & Characteristics**
- Graph type: primarily directed
- Output: residual network
- Complexity: `O(V · E²)`
- Strengths: clear process, easy to explain
- Note: May be slow on large dense networks

---

### 2. shortest_augmenting_path — Shortest Augmenting Path Max Flow

**Description**  
Uses distance labels to find relatively short augmenting paths, computing maximum flow with a more efficient augmentation strategy.

**Product Value**
- Typically outperforms Edmonds‑Karp on medium to large networks.
- Suitable for performance‑oriented max flow calculations.
- Useful for capacity planning, routing analysis, and network throughput assessment.

**Typical Scenarios**
- Telecom network maximum throughput analysis.
- Urban traffic backbone capacity calculation.
- Multi‑link logistics network capacity assessment.
- Service invocation network maximum request throughput estimation.

**Key Parameters**
- `s`: source node
- `t`: sink node
- `capacity`: edge capacity attribute
- `two_phase`: can improve performance on networks with many unit‑capacity edges
- `cutoff`: early termination threshold

**Applicability & Characteristics**
- Graph type: primarily directed
- Output: residual network
- Complexity: typical upper bound `O(V² · E)`
- Characteristics: generally more suitable for larger networks than Edmonds‑Karp

---

### 3. preflow_push — Push‑Relabel Max Flow

**Description**  
Push‑relabel algorithms do not maintain flow conservation at intermediate nodes during computation; nodes may temporarily hold “excess flow”. Flow is advanced toward the sink or pushed back via push and relabel operations, eventually yielding a maximum flow.

**Product Value**
- Suitable for complex, dense, highly connected networks.
- Often performs well in practice.
- Can compute only the maximum flow value to reduce output cost.

**Typical Scenarios**
- Data center network max flow analysis.
- Chip routing and complex interconnection networks.
- High‑concurrency communication network capacity assessment.
- Large‑scale infrastructure network bottleneck analysis.

**Key Parameters**
- `s`: source node
- `t`: sink node
- `capacity`: edge capacity attribute
- `global_relabel_freq`: global relabeling frequency; important performance tuning parameter
- `value_only`: whether to return only the max flow value

**Applicability & Characteristics**
- Graph type: primarily directed
- Output: residual network or max flow value
- Complexity: typical upper bound `O(V² · E)`
- Characteristics: often outperforms augmenting‑path algorithms in practice

---

### 4. capacity_scaling — Capacity Scaling Min‑Cost Flow

**Description**  
Uses a capacity scaling approach to handle flow problems with capacities, supplies/demands, and costs. The algorithm gradually refines the available flow at different capacity scales, seeking a flow that meets node demands while minimizing total cost.

**Product Value**
- Supports resource scheduling with “supply‑demand balance + cost optimality”.
- Suitable for min‑cost flow modeling in multi‑source multi‑sink networks.
- Simultaneously respects capacity limits and unit transportation costs.

**Typical Scenarios**
- Logistics dispatch: multiple warehouses supplying multiple stores, minimizing transport cost.
- Cloud resource allocation: distributing capacity from resource pools to business units.
- Energy networks: cost‑optimal transmission from generation to consumption.
- Supply chain networks: optimizing allocation under supply‑demand constraints.

**Key Parameters**
- `demand`: node demand attribute (negative for supply, positive for demand)
- `capacity`: edge capacity attribute
- `weight`: unit flow cost
- `heap`: priority queue parameter affecting performance

**Applicability & Characteristics**
- Graph type: primarily directed
- Output: min‑cost flow solution
- Suitable for: scheduling problems with capacities, supplies/demands, and costs
- Note: Total supply and total demand usually must balance; otherwise no feasible solution.

---

### 5. gomory_hu_tree — Gomory‑Hu Cut Tree

**Description**  
Represents all‑pairs minimum cut values of an undirected graph as a tree. For any two nodes, the minimum edge weight along the unique path in the tree equals the minimum cut value between those nodes in the original graph.

**Product Value**
- Compresses all‑pairs min cut structure into a single tree.
- Supports fast queries of minimum cut values between any two nodes.
- Suitable for global network resilience analysis and weakest‑link identification.

**Typical Scenarios**
- Communication networks: quickly query the minimum disconnection capacity between any two nodes.
- Transportation networks: identify the most fragile regional connections.
- Supply chain networks: assess supply interruption risk between enterprises or regions.
- Infrastructure networks: global resilience profiling and vulnerable link identification.

**Key Parameters**
- `capacity`: edge capacity attribute
- `flow_func`: underlying max flow algorithm (e.g., `edmonds_karp`, `shortest_augmenting_path`, `preflow_push`)

**Applicability & Characteristics**
- Graph type: undirected
- Output: Gomory‑Hu tree
- Complexity: typically requires `n - 1` max flow computations
- Note: Ideal when many pairwise min cut queries are needed; not cost‑effective for a single pair.

---

### 6. edge_expansion — Edge Expansion

**Description**  
Measures the boundary connection strength of a node set to the outside. Typically defined as the number (or weight) of edges from the set to the external complement, normalized by the size of the set (or other normalization).

**Product Value**
- Assesses whether a community or subgraph has a clear boundary.
- Quantifies outward diffusion or external exposure of a node set.
- Useful for community quality, cut structure, and network expansion analysis.

**Typical Scenarios**
- Community analysis: determine if a community is too loosely connected to the outside.
- Risk control: evaluate whether a gang is relatively closed.
- Network security: identify subnets with large exposure.
- Propagation analysis: measure the outward spreading potential of a group.
- Graph partition evaluation: compare boundary tightness across partitions.

**Key Parameters**
- `S`: node set to analyze
- `T`: optional external target set
- `weight`: edge weight attribute for weighted expansion
- `edge_boundary`: can specify how boundary edges are computed

**Applicability & Characteristics**
- Graph type: undirected / directed
- Output: numeric expansion value
- Note: High edge expansion indicates strong external connection; low edge expansion suggests a clearer, more closed boundary.

---

### 7. node_boundary — Node Boundary

**Description**  
Returns the set of external nodes that are adjacent to the given node set. That is, given an internal set `S`, find all nodes not in `S` that have at least one edge connecting to `S`.

**Product Value**
- Quickly identifies external contact points of a subgraph, community, or gang.
- Supports risk spillover, propagation entry points, attack surface, and cross‑layer connection analysis.
- Provides a basic capability for graph expansion, community boundary, and cut analysis.

**Typical Scenarios**
- Risk control networks: find peripheral accounts adjacent to a gang.
- Social networks: identify users connecting a community to the outside.
- Supply chain networks: identify external customers or upstream parties of a supplier group.
- Security networks: identify externally exposed nodes of a subnet.
- Opinion propagation: detect contact points where information spreads from a circle outward.

**Key Parameters**
- `nbunch1`: internal node set
- `nbunch2`: optional candidate external node set
- Graph direction: in directed graphs, boundary interpretation must consider edge direction.

**Applicability & Characteristics**
- Graph type: undirected / directed
- Output: set of nodes
- Characteristics: result is intuitive, suitable for boundary interpretation and downstream filtering.

---

## V. Recommended Usage Guide (Selection Advice)

- **Compute s → t max flow on small/medium graphs with emphasis on interpretability**: use `edmonds_karp`
- **Faster max flow on larger networks**: use `shortest_augmenting_path`
- **Very dense, complex, highly connected network max flow**: use `preflow_push`
- **Supply‑demand balance with unit cost constraints**: use `capacity_scaling`
- **Support any‑pair min cut queries efficiently**: use `gomory_hu_tree`
- **Evaluate how strongly a community or node set is connected externally**: use `edge_expansion`
- **Find external contact points / boundary nodes of a node set**: use `node_boundary`

---

## VI. Typical Questions That Can Be Answered Directly

- “What is the maximum carrying capacity from A to B in this network?”
- “Which links become bottlenecks under maximum flow?”
- “What is the lowest‑cost dispatch solution given current supply/demand and capacities?”
- “What is the minimum capacity to cut between any two nodes?”
- “Which pair of nodes represents the most vulnerable connection in the entire network?”
- “Is this community tightly connected to the outside?”
- “What are the external contact nodes of a certain gang?”
- “Who are the boundary nodes of this subnet?”
- “Which node set has the lowest edge expansion, indicating it is most like a closed community?”

---

## VII. Engineering Implementation Considerations

1. **Capacity field must be explicit**
   - Max flow and cut‑tree algorithms rely on a `capacity` attribute.
   - If an edge lacks a capacity attribute, algorithms may default to a fallback value or raise an error; product layer must define the rule.

2. **Interpret max flow results with the residual network**
   - The max flow value answers “what is the maximum flow”.
   - The residual network reveals which edges are saturated and which still have spare capacity.

3. **Different max flow algorithms suit different scales**
   - `edmonds_karp` is interpretable and good for small/medium graphs.
   - `shortest_augmenting_path` is better for larger graphs.
   - `preflow_push` often excels on dense, complex networks.

4. **Min‑cost flow requires supply‑demand balance**
   - `capacity_scaling` typically requires total supply equal total demand.
   - If unbalanced, add a dummy source/sink or adjust demands.

5. **Gomory‑Hu Tree is best for repeated queries**
   - Building the cut tree is expensive (requires multiple max flow computations).
   - For a single pair, a direct min‑cut or max‑flow computation is more efficient.
   - For many all‑pair queries, the cut tree is very valuable.

6. **Boundary metrics differ from max flow**
   - `edge_expansion` measures external connection strength of a set.
   - `node_boundary` returns adjacent external nodes.
   - These do not compute max flow but are excellent for community boundaries, diffusion boundaries, and risk spillover analysis.

7. **Directed graphs require care with direction**
   - In directed graphs, boundaries may differ between outgoing edges (from set) and incoming edges (to set).
   - Product documentation should clarify whether edges are considered undirected, outgoing only, or incoming only.

8. **Do not confuse capacity with cost semantics**
   - `capacity` indicates maximum flow volume.
   - `weight` in min‑cost flow indicates unit flow cost.
   - The same edge may have both a capacity and a cost representing different business meanings.

---

## VIII. Operator List

| No. | Operator Name                    | Description (Chinese)                        |
|-----|----------------------------------|----------------------------------------------|
| 1   | `edmonds_karp`                   | Edmonds‑Karp maximum flow algorithm          |
| 2   | `shortest_augmenting_path`       | Shortest augmenting path max flow algorithm  |
| 3   | `preflow_push`                   | Push‑relabel maximum flow algorithm          |
| 4   | `capacity_scaling`               | Capacity scaling minimum cost flow algorithm |
| 5   | `gomory_hu_tree`                 | Gomory‑Hu all‑pairs minimum cut tree         |
| 6   | `edge_expansion`                 | Edge expansion                               |
| 7   | `node_boundary`                  | Node boundary                                |