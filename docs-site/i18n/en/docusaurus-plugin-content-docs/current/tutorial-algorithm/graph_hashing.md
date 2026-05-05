---
sidebar_position: 18
---

# Graph Hashing Operator Set

**Operator Category**: Graph Hashing (Graph hashing, structural fingerprints and graph structure summaries)

**Algorithm Count**: 1

**Applicable Stages**: Graph structure deduplication, fast graph structure comparison, graph sample indexing, subgraph pattern retrieval, graph version comparison, structural fingerprint generation, graph similarity pre-filtering, graph data caching and incremental computation

**Product Positioning**: Provides graph hashing capability base for answering "what is the structural fingerprint of this graph / whether two graphs might have the same structure / how to quickly deduplicate graphs / how to generate an indexable summary representation of graph structure"

---

## 1. Operator Set Overview

The Graph Hashing operator set focuses on **generating stable, compact, and comparable hash representations for graph structures**, mainly answering the following questions:

1. **Graph Structural Fingerprint Generation**

   * How to convert a graph into a fixed-length or compact structural summary?
   * How to represent graph topology with hash values?
   * How to establish indexable and cacheable structural identifiers for graph samples?

2. **Fast Graph Structure Comparison**

   * Do two graphs possibly have the same structure?
   * Can two graphs be pre-filtered by hash values first?
   * In large batches of graph samples, how to quickly locate structurally duplicate or near-duplicate graphs?

3. **Graph Data Deduplication and Version Comparison**

   * Do graphs from different sources have duplicate structures?
   * Has the graph structure changed before and after updates?
   * Has the subgraph extraction result already been processed?

4. **Graph Pattern Retrieval and Caching**

   * How to generate reusable identifiers for subgraph patterns?
   * How to establish cache keys for graph computation results?
   * How to reduce redundant computation and redundant analysis?

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operator | Function Description |
| --- | --- | --- |
| Weisfeiler-Lehman Graph Hashing | `weisfeiler_lehman_graph_hash` | Generate structural hash values for graphs based on Weisfeiler-Lehman color refinement ideas |

---

## 3. General Input and Output Conventions

* **Input `G`**: NetworkX Graph / DiGraph
* **Common Input Parameters**:

  * `node_attr`: Node attribute name to include node attributes in hash calculation
  * `edge_attr`: Edge attribute name to include edge attributes in hash calculation
  * `iterations`: Number of WL color refinement iterations
  * `digest_size`: Hash digest length
* **Common Output Types**:

  * Graph hash string: `str`
* **Result Interpretation**:

  * Same hash value: Two graphs likely have the same or highly consistent structural representation
  * Different hash values: Two graphs can usually be determined as different under the current hash perspective or specified attributes

---

## 4. Detailed Operator Descriptions

### 1. weisfeiler_lehman_graph_hash —— Weisfeiler-Lehman Graph Hashing

**Function Description**
Based on Weisfeiler-Lehman color refinement ideas, generate a structural hash value for a graph. The algorithm starts from node labels or attributes, repeatedly aggregates neighbor information and updates node representations, and finally encodes the overall graph structure into a hash digest.

**Product Value**

* Generate stable structural fingerprints for graphs
* Support fast deduplication and indexing of large batches of graph samples
* Can serve as a pre-filtering step for graph isomorphism, graph similarity or structure matching
* Support incorporating node attributes and edge attributes into structural summaries to enhance business semantic expressiveness

**Typical Scenarios**

* Graph structure deduplication
* Subgraph pattern caching
* Graph version change detection
* Large-scale graph sample indexing
* Molecular graph structural fingerprint generation
* Fraud gang structural pattern retrieval
* Workflow / flowchart structure comparison
* Knowledge graph local structure summary

**Key Parameters**

* `node_attr`: Specify node attributes as initial node labels
* `edge_attr`: Specify edge attributes to participate in neighbor aggregation
* `iterations`: Control neighbor information propagation and structure awareness range
* `digest_size`: Control output hash digest length

**Applicability and Characteristics**

* Graph Type: Directed / Undirected graphs
* Output: Graph structural hash string
* Characteristics: Relatively efficient computation, suitable for batch graph structure summaries and fast comparison
* Note: Hash values are usually used for fast pre-filtering and should not completely replace strict graph isomorphism judgment

---

## 5. Recommended Usage Guide

### 1. Graph Structural Fingerprint Generation

* Generate structural hash for a single graph: `weisfeiler_lehman_graph_hash`
* Generate summaries for subgraphs, pattern graphs or local relationship circles: `weisfeiler_lehman_graph_hash`

### 2. Graph Structure Deduplication

* Batch compute graph hash values
* Group graphs with the same hash value as candidate duplicate structures
* Further confirm candidate results by combining business fields or isomorphism algorithms

### 3. Graph Version Comparison

* Compute hash values for graphs before and after updates respectively
* Different hash values usually indicate changes in structure or specified attributes
* Same hash values can serve as quick judgment basis that structure has not significantly changed

### 4. Caching and Indexing

* Use graph hash as cache key for graph computation results
* Use subgraph hash as index for pattern retrieval or deduplication
* Reduce redundant graph analysis, redundant subgraph extraction and redundant model computation

### 5. Scenario-based Selection Suggestions

* **Want to generate structural fingerprints for graphs**: `weisfeiler_lehman_graph_hash`
* **Want to quickly determine whether two graphs might be the same**: `weisfeiler_lehman_graph_hash`
* **Want to deduplicate large batches of graphs**: `weisfeiler_lehman_graph_hash`
* **Want to establish indexes for subgraph patterns**: `weisfeiler_lehman_graph_hash`
* **Want to determine whether graph structure changes before and after updates**: `weisfeiler_lehman_graph_hash`
* **Want to use as pre-filtering before graph isomorphism judgment**: `weisfeiler_lehman_graph_hash`

---

## 6. Engineering and Usage Notes

1. **Graph hashing is suitable for pre-filtering, not equivalent to strict isomorphism proof**
   Same hash values usually indicate two graphs are highly consistent under the current hash perspective, but it is still recommended to combine with algorithms like `is_isomorphic` for precise confirmation in critical scenarios.

2. **Node and edge attributes affect hash semantics**
   After specifying `node_attr` or `edge_attr`, the hash reflects not only topology but also corresponding attributes. Missing attributes, inconsistent types or naming inconsistencies all affect results.

3. **iterations determines structure awareness range**
   More iterations allow nodes to aggregate information from farther neighborhoods; but too many iterations also increase computational cost and may bring over-smoothing effects on summaries.

4. **digest_size affects summary length and collision risk**
   Larger digest lengths usually reduce collision probability but also increase storage length. For large-scale indexing scenarios, choose appropriate values based on collision tolerance.

5. **Graph preprocessing should remain consistent**
   When doing version comparison, deduplication or batch indexing, maintain consistency in node attributes, edge attributes, directionality, multi-edge handling methods and parameter configurations, otherwise hash results are not comparable.

6. **Directed and undirected graphs have different semantics**
   If the same set of edges are computed separately as directed and undirected graphs, the hash semantics differ. Need to choose graph type based on whether business relationships have directionality.

---

## 7. Typical Directly Answerable Questions

* "What is the structural hash of this graph?"
* "Are the structural fingerprints of these two graphs consistent?"
* "Are there duplicate structures in this batch of subgraphs?"
* "Has the graph structure changed before and after updates?"
* "Can we generate a structural fingerprint for each gang subgraph?"
* "Can we use hash values to quickly filter out possibly isomorphic graphs?"
* "Is this flowchart and template graph possibly the same structure?"
* "Has this knowledge graph local structure already been processed?"
* "Can we cache graph computation results by structural hash?"
* "Does the graph hash change after node attributes and edge attributes participate?"

---

## 8. Operator Checklist

| No. | Operator Name | English Description |
|---:|---|---|
| 1 | `weisfeiler_lehman_graph_hash` | Weisfeiler-Lehman Graph Structural Hashing |