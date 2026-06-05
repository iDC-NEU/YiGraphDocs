---
sidebar_position: 2
---

# Path Operator Set

**Operator Category**: Path (Path, Reachability, Tour and Critical Path)

**Algorithm Count**: 19

**Applicable Stages**: Route planning, dependency chain analysis, reachability determination, global distance evaluation, tour/traversal design, path enumeration, heuristic pathfinding, cycle detection

**Product Positioning**: Provides a unified path capability base for answering "how to go shortest/most economical", "is there a reachable path", "what are the feasible paths", "how to traverse all edges", "is there a cycle", "what is the longest dependency chain/critical path in a DAG", and "does a Hamiltonian path exist".

---

## 1. Operator Set Overview

The Path operator set targets various graph structures, including traffic road networks, communication topologies, call dependencies, transaction links, knowledge citation networks, task flowcharts, inspection networks, etc., covering the following core problems:

1. **Shortest Path Calculation**
   - What is the shortest path between two nodes?
   - What is the shortest distance/cost?
   - Do we need to support non-negative weights, negative weights, or heuristic search?

2. **Global Distance Evaluation**
   - What is the shortest distance between any two nodes?
   - What is the average path length of the entire network?
   - Which all-pairs shortest path algorithm should be chosen for dense vs. sparse graphs?

3. **Reachability and Path Enumeration**
   - Can A reach B?
   - What simple paths exist from A to B?
   - What are multiple alternative shortest simple paths?

4. **DAG Critical Path Analysis**
   - What is the longest dependency chain in a DAG?
   - What is the total length of the critical path?

5. **Eulerian Paths and Tours**
   - Can we traverse each edge exactly once?
   - Does an Eulerian path or Eulerian circuit exist?
   - How to generate the corresponding edge traversal order?

6. **Cycles and Special Paths**
   - Does a cycle exist in the graph?
   - Does a Hamiltonian path exist?
   - How to find or verify complex path structures?

---

## 2. Capability Classification and Selection Suggestions

| Objective | Recommended Operators | When to Use |
|---|---|---|
| Detect cycles in graph | `find_cycle` | Need to quickly discover cycle risks in dependency graphs, call graphs, flowcharts |
| Shortest "path sequence" from A→B | `shortest_path` / `dijkstra_path` / `bellman_ford_path` / `astar_path` | Need to output specific nodes traversed |
| Shortest "distance value" from A→B | `dijkstra_path_length` | Only care about shortest distance, lowest cost, shortest time |
| Non-negative weight shortest path | `dijkstra_path`, `dijkstra_path_length` | All edge weights are non-negative, e.g., distance, time, cost |
| Heuristic shortest path search | `astar_path` | Have heuristic function available in road networks, maps, spatial search |
| Possible negative weight edges | `bellman_ford_path` / `goldberg_radzik` | Cost model includes subsidies, rebates, negative revenue; no negative cycles allowed |
| All-pairs shortest path, dense graph | `floyd_warshall` | Moderate node scale, many edges, need complete distance matrix |
| All-pairs shortest path, sparse graph | `johnson` | Large-scale sparse graph, may contain negative weights but no negative cycles |
| Network average shortest path length | `average_shortest_path_length` | Measure network efficiency, average hops, small-world characteristics |
| Determine if A can reach B | `has_path` | Only need Yes/No, don't need specific path |
| Enumerate all simple paths | `all_simple_paths` | Need to analyze all non-repeating node feasible paths |
| Output multiple shortest simple paths | `shortest_simple_paths` | Need alternative routes, candidate links, Top-K path schemes |
| DAG critical path | `dag_longest_path`, `dag_longest_path_length` | Project scheduling, dependency chains, process time analysis |
| Determine if Eulerian circuit exists | `is_eulerian` | Inspection, road coverage, edge-by-edge traversal pre-judgment |
| Generate Eulerian path | `eulerian_path` | Traverse each edge exactly once, no requirement to return to start |
| Generate Eulerian circuit | `eulerian_circuit` | Traverse each edge exactly once and return to start |
| Find Hamiltonian path | `hamiltonian_path` | Need path structure visiting each node exactly once |

---

## 3. General Input and Output Conventions

- **Input `G`**: NetworkX graph object
  - Can be undirected graph `Graph`
  - Can be directed graph `DiGraph`
  - Some algorithms support `MultiGraph` / `MultiDiGraph`

- **Common Parameters**
  - `source`: Starting node
  - `target`: Ending node
  - `weight`: Edge weight field name or weight function
  - `cutoff`: Maximum search depth or maximum path length
  - `heuristic`: Heuristic function in A* algorithm
  - `keys`: Whether to return edge key in multigraph

- **Weight Semantics**
  - Shortest path category: `weight` usually represents distance, cost, time; smaller is better
  - DAG longest path category: `weight` usually represents duration, revenue, cumulative cost; larger is "longer"

- **Common Output**
  - Path: Node sequence `list[node]`
  - Distance: Numeric distance / cost
  - All-pairs shortest path: `dict[source][target] -> dist/path`
  - Eulerian path / circuit: Edge sequence iterator
  - Reachability / judgment: `bool`
  - Path enumeration: Path iterator

> Note: In multigraphs, parallel edges may represent different roads, links, or transaction relationships. If specific edges need to be distinguished, combine edge keys or edge attributes.

---

## 4. Detailed Operator Descriptions

### 1. find_cycle —— Cycle Detection

**Function Description**  
Find a cycle in the graph. If a cycle exists, return the edge sequence forming the cycle; if not, typically throw a no-cycle exception or return empty result.

**Product Value**
- Quickly discover dependency cycles, call cycles, process loops
- Important diagnostic capability before DAG validation and topological sorting
- Help locate critical relationships preventing linear process execution

**Typical Scenarios**
- Project task circular dependency detection
- Data lineage loop detection
- Service call cycle detection
- Approval process loop investigation
- Knowledge reference cycle analysis

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: Edge sequence
- Complexity: Usually `O(V + E)`

---

### 2. shortest_path —— General Shortest Path

**Function Description**  
Calculate shortest path between two nodes, from single source to multiple nodes, or between all sources. In unweighted graphs, count by number of edges; in weighted graphs, use specified `weight` field or function.

**Product Value**
- Provide unified shortest path entry point
- Flexibly return single-pair, single-source, or all-source results based on whether `source`, `target` are specified
- Suitable as default path query capability in products

**Typical Scenarios**
- Shortest route from A to B
- Shortest explanation path of dependency chain
- Minimum hop path in network routing
- Shortest association link between entities in knowledge graph

**Key Parameters**
- `source`: Starting point, optional
- `target`: Ending point, optional
- `weight`: Edge weight field or function
- `method`: Can choose `dijkstra` or `bellman-ford` for weighted scenarios

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: Node path or path dictionary
- Complexity: Depends on whether weighted and query scope

---

### 3. dijkstra_path —— Dijkstra Shortest Path

**Function Description**  
In graphs with non-negative edge weights, calculate shortest path from `source` to `target` and return node sequence.

**Product Value**
- One of the most commonly used weighted shortest path algorithms in engineering
- Stable performance for non-negative weight scenarios
- Intuitive path results, easy to display and explain

**Typical Scenarios**
- Logistics path planning
- Minimum latency routing in communication networks
- Shortest time path in traffic networks
- Minimum cost service call path

**Key Parameters**
- `source`: Starting point
- `target`: Ending point
- `weight`: Distance, time, cost, or other edge weight field

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Requirement: Non-negative edge weights
- Output: Node sequence
- Complexity: Typically `O(E + V log V)`

---

### 4. dijkstra_path_length —— Dijkstra Shortest Path Length

**Function Description**  
Calculate shortest path length from `source` to `target`, returning only distance or cost value, not the specific path.

**Product Value**
- Suitable for scenarios needing only metric values, not route details
- Lighter result compared to returning complete path
- Can be used for batch distance evaluation or threshold judgment

**Typical Scenarios**
- Two-point shortest distance query
- Minimum logistics cost estimation
- Minimum latency evaluation
- Risk propagation shortest hop evaluation

**Key Parameters**
- `source`: Starting point
- `target`: Ending point
- `weight`: Edge weight field or function

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Requirement: Non-negative edge weights
- Output: Numeric distance
- Complexity: Typically `O(E + V log V)`

---

### 5. bellman_ford_path —— Bellman-Ford Shortest Path

**Function Description**  
Calculate shortest path from `source` to `target`, supporting negative weight edges, but no negative weight cycles reachable from source.

**Product Value**
- Applicable to scenarios with subsidies, rebates, negative costs in weight model
- Alternative when Dijkstra is not applicable
- Handle more complex cost modeling

**Typical Scenarios**
- Cost model with rebates or subsidies
- Net cost path in financial transactions
- Negative revenue modeling in risk control chains
- Path calculation with penalties and rewards

**Key Parameters**
- `source`: Starting point
- `target`: Ending point
- `weight`: Edge weight field, default usually `"weight"`

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Support: Negative weight edges
- Not support: Negative weight cycles
- Output: Node sequence
- Complexity: Approximately `O(VE)`

---

### 6. goldberg_radzik —— Goldberg-Radzik Shortest Path

**Function Description**  
Goldberg-Radzik is an algorithm for single-source shortest path problem, handling negative weight edges and detecting negative weight cycles.

**Product Value**
- Suitable for single-source shortest path with negative weight edges
- Better practical performance than traditional Bellman-Ford on some graph structures
- Suitable for path calculation and anomaly detection in complex cost networks

**Typical Scenarios**
- Transaction networks with negative costs
- Route cost analysis with subsidies
- Path optimization with mixed rewards and penalties
- Negative weight cycle risk detection

**Key Parameters**
- `source`: Starting point
- `weight`: Edge weight field or function

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Support: Negative weight edges
- Can detect: Negative weight cycles
- Output: Distance and predecessor information
- Complexity: Worst case still potentially high, suitable for medium-scale negative weight graph analysis

---

### 7. floyd_warshall —— Floyd-Warshall All-pairs Shortest Path

**Function Description**  
Based on dynamic programming, calculate shortest path distances between any two nodes at once, returning complete distance structure.

**Product Value**
- Suitable for scenarios needing complete distance matrix
- Intuitive and stable for dense graphs
- Can serve as foundation for global distance analysis

**Typical Scenarios**
- Global shortest distance matrix between cities
- All-pairs distance analysis in small-scale networks
- Overall reachability cost evaluation in road networks
- Pre-processing for graph layout or similarity calculation

**Key Parameters**
- `weight`: Edge weight field name, default usually `"weight"`

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: `dict[source][target] -> distance`
- Complexity: `O(V^3)`
- Suitable for: Medium-scale nodes, dense edges

---

### 8. johnson —— Johnson All-pairs Shortest Path

**Function Description**  
Johnson algorithm calculates all-pairs shortest paths in sparse graphs, handling negative weight edges but not negative weight cycles.

**Product Value**
- Suitable for large-scale sparse graph any-pair path calculation
- Usually more efficient than Floyd-Warshall on sparse graphs
- Can be used for global path analysis with negative weights

**Typical Scenarios**
- Large-scale transportation networks
- All-pairs routing analysis in communication networks
- Global path analysis of entities in knowledge graphs
- Sparse transaction networks with negative weights

**Key Parameters**
- `weight`: Edge weight field name or function

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Support: Negative weight edges
- Not support: Negative weight cycles
- Output: All-pairs shortest path dictionary
- Complexity: Approximately `O(VE + V^2 log V)`

---

### 9. average_shortest_path_length —— Average Shortest Path Length

**Function Description**  
Calculate average value of shortest path lengths between all node pairs, measuring overall network connectivity efficiency or average hop count.

**Product Value**
- Reflect overall network reachability efficiency
- Can be used to determine if network has "small-world" characteristics
- Common global metric in complex network analysis

**Typical Scenarios**
- Average interpersonal distance in social networks
- Average transmission hops in communication networks
- Average travel cost in transportation networks
- Average association distance in knowledge networks
- Efficiency evaluation in organizational collaboration networks

**Key Parameters**
- `weight`: Whether to use edge weights
- `method`: Can choose unweighted, multiple Dijkstra, Bellman-Ford, Floyd-Warshall, etc.

**Applicability and Characteristics**
- Graph Type: Usually requires connected graph; directed graphs usually require strong connectivity
- Output: Numeric average distance
- Complexity: Depends on underlying shortest path method

---

### 10. has_path —— Reachability Determination

**Function Description**  
Determine whether a path exists from `source` to `target` in the graph.

**Product Value**
- Quick answer to "can we reach"
- No need to calculate complete path, suitable for high-frequency queries
- Can serve as pre-filter for path calculation

**Typical Scenarios**
- Whether service A can call service B
- Whether fund account can flow to target account
- Whether one task will affect another task
- Whether one knowledge point can be derived to another

**Key Parameters**
- `source`: Starting point
- `target`: Ending point

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: `bool`
- Complexity: Worst case `O(V + E)`

---

### 11. dag_longest_path —— DAG Longest Path

**Function Description**  
In a Directed Acyclic Graph (DAG), calculate longest path and return corresponding node sequence.

**Product Value**
- Identify longest dependency chain
- Support critical path analysis
- Can be used for project scheduling, process time, and task dependency optimization

**Typical Scenarios**
- Project management critical path
- Longest time-consuming chain in workflow
- Bottleneck chain in data processing pipeline
- Longest learning path in course prerequisite dependencies
- Maximum cumulative latency analysis in call chains

**Key Parameters**
- `weight`: Edge weight field, representing duration, cost, or revenue
- `default_weight`: Default weight for unweighted edges
- `topo_order`: Can pass existing topological order to reuse computation

**Applicability and Characteristics**
- Graph Type: DAG
- Output: Node sequence
- Complexity: `O(V + E)`

---

### 12. dag_longest_path_length —— DAG Longest Path Length

**Function Description**  
Calculate cumulative length or weight of longest path in DAG, returning only length value, not specific path.

**Product Value**
- Quickly get critical path total time or cost
- Suitable for metric dashboards, risk assessment, scheduling estimation
- Can be used together with `dag_longest_path`

**Typical Scenarios**
- Project minimum completion time estimation
- Process maximum time evaluation
- Dependency chain maximum cumulative risk
- Longest propagation distance in DAG network

**Key Parameters**
- `weight`: Edge weight field
- `default_weight`: Default edge weight

**Applicability and Characteristics**
- Graph Type: DAG
- Output: Numeric length
- Complexity: `O(V + E)`

---

### 13. is_eulerian —— Eulerian Graph Determination

**Function Description**  
Determine whether graph has an Eulerian circuit, i.e., whether we can start from a node, traverse each edge exactly once, and return to starting point.

**Product Value**
- Pre-validation before generating Eulerian circuit
- Can determine if inspection network supports closed-loop one-time coverage
- Support road, line, pipeline, link coverage analysis

**Typical Scenarios**
- Road inspection closed-loop planning
- Pipeline inspection path design
- Chinese Postman Problem pre-judgment
- Network link full coverage traversal
- Circuit wiring check

**Intuitive Understanding**
- Undirected graph: All relevant nodes have even degree and are connected
- Directed graph: Each node's in-degree equals out-degree and satisfies connectivity conditions

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: `bool`
- Complexity: `O(V + E)`

---

### 14. eulerian_path —— Eulerian Path

**Function Description**  
When conditions are met, generate a path traversing each edge exactly once, without requiring start and end nodes to be the same.

**Product Value**
- Suitable for scenarios needing to cover all edges but not forcing return to start
- Can be used for inspection, cleaning, link tracking tasks
- Directly output executable edge traversal order

**Typical Scenarios**
- Road cleaning path
- Segment-by-segment pipeline inspection
- Edge-by-edge transaction link investigation
- Network link coverage testing
- One-stroke drawing path generation

**Key Parameters**
- `source`: Optional starting point
- `keys`: Whether to return edge key in multigraph

**Applicability and Characteristics**
- Graph Type: Directed / Undirected / some multigraph
- Output: Edge sequence iterator
- Complexity: Usually `O(E)`

---

### 15. eulerian_circuit —— Eulerian Circuit

**Function Description**  
Generate an Eulerian circuit: starting from specified or any starting point, traverse each edge exactly once, and finally return to starting point.

**Product Value**
- Suitable for closed-loop inspection and closed-loop coverage tasks
- Can output complete edge visit order
- Commonly used for path planning, line inspection, topology coverage

**Typical Scenarios**
- Closed-loop road inspection
- Power line full coverage inspection
- Communication link testing
- Logistics vehicle return-to-warehouse route
- Graph structure one-stroke closed-loop analysis

**Key Parameters**
- `source`: Specify starting point
- `keys`: Whether to output edge key in multigraph

**Applicability and Characteristics**
- Graph Type: Directed / Undirected / Multigraph
- Output: Edge sequence iterator
- Complexity: Usually `O(E)`

---

### 16. astar_path —— A* Heuristic Shortest Path

**Function Description**  
Use A* algorithm to calculate shortest path from `source` to `target`. A* introduces heuristic function based on Dijkstra to prioritize search in directions more likely to approach target.

**Product Value**
- Usually more efficient than plain Dijkstra in spatial road networks or map search
- Support combining business heuristic information for path search
- Suitable for scenarios with clear targets and reasonable distance estimation functions

**Typical Scenarios**
- Map navigation
- Game pathfinding
- Robot path planning
- City road network search
- Spatial knowledge graph path search

**Key Parameters**
- `source`: Starting point
- `target`: Ending point
- `heuristic`: Heuristic function estimating remaining cost from current node to target
- `weight`: Edge weight field or function

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Requirement: Heuristic function should not overestimate true distance to guarantee optimality
- Output: Node sequence
- Complexity: Depends on heuristic function quality; worst case can degrade to Dijkstra-like search

---

### 17. all_simple_paths —— All Simple Paths Enumeration

**Function Description**  
Enumerate all simple paths from `source` to `target`. Simple path means path without repeating same node.

**Product Value**
- Analyze all feasible relationship chains between two points
- Support full enumeration of risk propagation paths, fund paths, dependency paths
- Suitable for small to medium graphs or path exploration with depth limit

**Typical Scenarios**
- All fund chains between two accounts
- All call chains between two services
- All knowledge association paths between two entities
- Supply chain alternative path analysis
- Risk transmission path enumeration

**Key Parameters**
- `source`: Starting point
- `target`: Ending point
- `cutoff`: Maximum path length, recommended to set in engineering

**Applicability and Characteristics**
- Graph Type: Directed / Undirected graph
- Output: Path iterator
- Note: Path count may grow exponentially
- Suitable for: Small graphs or limited depth path analysis

---

### 18. shortest_simple_paths —— Shortest Simple Paths Enumeration

**Function Description**  
Generate simple path sequence from `source` to `target` sorted by path length or weight from short to long. Commonly used to get Top-K alternative paths.

**Product Value**
- Not just give one shortest path, but generate multiple candidate paths by priority
- Suitable for route alternatives, link disaster recovery, and scheme comparison
- More suitable than full enumeration for taking Top-K results on demand

**Typical Scenarios**
- Multiple alternative routes in navigation
- Backup paths in network routing
- Supply chain alternative schemes
- Shortest suspicious link candidates in risk investigation
- Service call chain disaster recovery path analysis

**Key Parameters**
- `source`: Starting point
- `target`: Ending point
- `weight`: Edge weight field or function

**Applicability and Characteristics**
- Graph Type: Usually used for simple graphs
- Output: Path generator
- Usage suggestion: Combine with `itertools.islice` or product parameters to limit Top-K count

---

### 19. hamiltonian_path —— Hamiltonian Path

**Function Description**  
Find a path passing through each node in graph exactly once. Unlike Eulerian path focusing on "each edge once", Hamiltonian path focuses on "each node once".

**Product Value**
- Used for path planning visiting all nodes without repetition
- Can express one-time coverage order of tasks, sites, entities
- Suitable for special structure graphs or combinatorial path analysis on small to medium graphs

**Typical Scenarios**
- One-time site visit path
- Task node coverage order
- Tourism route planning
- Graph structure traversability analysis
- Competition/permutation path problems

**Applicability and Characteristics**
- Graph Type: Directed / Undirected, specific support depends on implementation
- Output: Node sequence
- Note: Hamiltonian path problem usually has high computational complexity; use cautiously for large graphs

---

## 5. Recommended Usage Guide

### 1. Shortest Path Query

- Default general query: `shortest_path`
- Non-negative weight shortest path: `dijkstra_path`
- Only need distance value: `dijkstra_path_length`
- Negative weight edges exist: `bellman_ford_path` / `goldberg_radzik`
- Have spatial heuristic function: `astar_path`

### 2. All-pairs Shortest Path and Global Distance

- Dense graph all-pairs distance: `floyd_warshall`
- Sparse graph all-pairs path: `johnson`
- Network average distance: `average_shortest_path_length`

### 3. Reachability and Path Enumeration

- Only determine reachability: `has_path`
- Enumerate all simple paths: `all_simple_paths`
- Get multiple candidate short paths: `shortest_simple_paths`

### 4. DAG Critical Path

- Output longest path node sequence: `dag_longest_path`
- Output only longest path length: `dag_longest_path_length`
- Recommend confirming graph is DAG before use: `is_directed_acyclic_graph`

### 5. Eulerian Paths and Circuits

- Determine if Eulerian circuit exists: `is_eulerian`
- Generate Eulerian path: `eulerian_path`
- Generate Eulerian circuit: `eulerian_circuit`

### 6. Cycles and Special Paths

- Find cycle in graph: `find_cycle`
- Find Hamiltonian path: `hamiltonian_path`

---

## 6. Typical Directly Answerable Questions

- "What is the shortest path from A to B? Please output node sequence."
- "What is the shortest path length from A to B?"
- "Does the graph have a cycle? If yes, please return one."
- "Is there a path from A to B in the graph?"
- "Please list all simple paths from A to B."
- "Please give the top 5 shortest alternative paths from A to B."
- "What is the average shortest path length of the network?"
- "Please calculate shortest paths between any two nodes."
- "In the DAG, which nodes are in the longest dependency chain?"
- "What is the total duration of the critical path?"
- "Does this graph have an Eulerian circuit?"
- "If an Eulerian path exists, please output the traversal order of each edge."
- "Does a Hamiltonian path exist that passes through all nodes without repetition?"
- "Can this road network be designed as an inspection route covering all edges once?"

---

## 7. Engineering Implementation Notes

1. **Weight semantics must be unified**
   - In shortest paths, weight usually represents distance, time, cost; smaller is better.
   - In DAG longest paths, weight usually represents duration, revenue, cumulative cost; larger is "longer".
   - When mixing multiple weight fields in same graph, clearly define field meanings.

2. **Carefully choose algorithm for negative weight edges**
   - Non-negative weights: Prioritize `dijkstra_path` / `dijkstra_path_length`
   - Negative weight edges exist: Use `bellman_ford_path` / `goldberg_radzik`
   - All-pairs with negative weights: Use `johnson`
   - When negative weight cycles exist, shortest path may be undefined.

3. **Choose all-pairs algorithm by graph scale**
   - Dense graph, medium node count: `floyd_warshall`
   - Sparse graph, large node count: `johnson`

4. **Control scale of path enumeration**
   - `all_simple_paths` may produce exponential results.
   - In engineering, recommend setting `cutoff` or limiting return count.
   - For multiple candidate paths, prioritize `shortest_simple_paths` to take Top-K on demand.

5. **Recommend pre-judgment for Eulerian algorithms**
   - First use `is_eulerian` or check degree conditions.
   - Then call `eulerian_circuit` or `eulerian_path`.
   - In multigraphs, note whether edge key needs to be returned.

6. **DAG longest path must ensure acyclicity**
   - `dag_longest_path` and `dag_longest_path_length` only apply to DAGs.
   - Recommend validating through DAG judgment capability before use.

7. **A* algorithm depends on heuristic function quality**
   - Better heuristic function closer to true remaining distance, higher search efficiency.
   - If heuristic function overestimates true distance, may affect optimality.
   - Without effective heuristic function, can fall back to Dijkstra.

8. **Hamiltonian path has high computation cost**
   - Hamiltonian path problem usually has high difficulty.
   - More suitable for small to medium graphs, special structure graphs, or heuristic analysis.
   - For large graphs, use cautiously; if necessary, combine with pruning or approximation methods.

---

## 8. Operator Checklist

| No. | Operator Name | Description |
|---:|---|---|
| 1 | `find_cycle` | Find a cycle in the graph |
| 2 | `shortest_path` | General shortest path |
| 3 | `dijkstra_path` | Dijkstra shortest path |
| 4 | `dijkstra_path_length` | Dijkstra shortest path length |
| 5 | `bellman_ford_path` | Bellman-Ford shortest path |
| 6 | `goldberg_radzik` | Goldberg-Radzik single-source shortest path |
| 7 | `floyd_warshall` | Floyd-Warshall all-pairs shortest path |
| 8 | `johnson` | Johnson all-pairs shortest path |
| 9 | `average_shortest_path_length` | Average shortest path length |
| 10 | `has_path` | Reachability determination |
| 11 | `dag_longest_path` | DAG longest path |
| 12 | `dag_longest_path_length` | DAG longest path length |
| 13 | `is_eulerian` | Eulerian graph determination |
| 14 | `eulerian_path` | Eulerian path |
| 15 | `eulerian_circuit` | Eulerian circuit |
| 16 | `astar_path` | A* heuristic shortest path |
| 17 | `all_simple_paths` | All simple paths enumeration |
| 18 | `shortest_simple_paths` | Shortest simple paths enumeration |
| 19 | `hamiltonian_path` | Hamiltonian path |
