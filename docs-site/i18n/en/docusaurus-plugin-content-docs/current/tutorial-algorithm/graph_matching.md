---
sidebar_position: 11
---

# Graph Matching Operator Set

**Operator Category**: Graph Matching (Graph structure matching, subgraph pattern containment, and tree isomorphism testing)

**Algorithm Count**: 2

**Applicable Stages**: Structural pattern recognition, subgraph template matching, tree structure alignment, hierarchical structure deduplication, pattern retrieval, structural consistency validation

**Product Positioning**: Provides a graph structure matching and pattern recognition capability base for answering "whether a graph contains a certain structural pattern / whether two trees are structurally consistent / whether structural matching can be achieved by node renaming"

---

## 1. Operator Set Overview

The Graph Matching operator set focuses on **structural correspondences between graphs**, mainly used to determine whether graph structures are identical, whether a certain pattern is contained, and whether tree structures can be matched one-to-one.

It mainly answers the following questions:

1. **Subgraph Pattern Containment**

   * Does a large graph contain a specified small graph pattern?
   * Does a certain structural template appear in the target graph?
   * Is there a structurally “weak match” or “pattern embedding”?

2. **Tree Structure Matching**

   * Do two trees have the same structure?
   * Even if node names differ, are the hierarchical relationships of the trees consistent?
   * Are two organizational structures, directory structures, or classification structures equivalent?

3. **Structural Template Recognition**

   * Does a business process conform to a standard template?
   * Does a local network structure match a known pattern?
   * Can a specific structural form be retrieved from a graph?

4. **Structural Deduplication and Merging**

   * Are two tree structures different only in node naming?
   * Are there duplicate patterns among multiple structural samples?
   * Can structurally identical graphs be grouped into the same category?

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operator | Function Description |
| --- | --- | --- |
| Subgraph Monomorphism Matching | `subgraph_is_monomorphic` | Determine whether the target graph contains a subgraph that is monomorphically matched with the pattern graph |
| Tree Isomorphism Testing | `tree_isomorphism` | Determine whether two trees are structurally equivalent and return node correspondences |

---

## 3. General Input and Output Conventions

* **Input `G` / `G1` / `G2`**

  * NetworkX Graph / DiGraph
  * Used to represent the graph to be matched, the target graph, or the pattern graph

* **Common Input Objects**

  * `G1`: The first graph or main graph
  * `G2`: The second graph or pattern graph
  * `root1` / `root2`: Specified root nodes in tree structures; may be needed in some tree matching scenarios

* **Common Output Types**

  * Judgment type: `bool`
  * Matching type: node mappings, node-pair lists, or matching relationships
  * Structural type: correspondence relationships between two graphs or two trees

---

## 4. Detailed Operator Descriptions

### 1. subgraph_is_monomorphic —— Subgraph Monomorphism Testing

**Function Description**
Determine whether a target graph contains a subgraph that satisfies a monomorphic matching relationship with a given pattern graph.

Monomorphic matching can be understood as a relatively relaxed structural matching method:

* Nodes in the pattern graph need to map to nodes in the target graph
* Edge relationships in the pattern graph need to be preserved in the target graph
* Compared with strict induced subgraph isomorphism, monomorphic matching usually cares more about “whether pattern edges exist” and does not force all extra edges to be exactly the same

That is, it is suitable for answering:

> “Has this structural pattern appeared in the large graph?”

Rather than strictly requiring:

> “Is this local structure exactly identical to the pattern graph, with no extra relationships?”

**Product Value**

* Supports structural pattern retrieval
* Can discover local structural templates in target graphs
* Suitable for rule recognition, pattern hits, and risk structure discovery
* More suitable than strict isomorphism for real business graphs, because business graphs often contain extra relationships

**Typical Scenarios**

* Gang pattern recognition in fraud networks
* Suspicious path or local structure detection in transaction graphs
* Pattern querying in knowledge graphs
* Template structure matching in flowcharts
* Local relationship pattern discovery in social networks
* Architecture pattern checks in system dependency graphs

**Applicability and Characteristics**

* Graph Type: Undirected / Directed graphs, depending on the specific matching object
* Input: Target graph and pattern graph
* Output: `bool`
* Characteristics: Suitable for determining “whether a certain structural pattern is contained”
* Note: Graph matching problems are usually computationally expensive; use cautiously for large graphs or complex patterns

---

### 2. tree_isomorphism —— Tree Isomorphism Testing

**Function Description**
Determine whether two trees are structurally equivalent.

If two trees can be made to have exactly the same connectivity relationships by renaming nodes, then they are isomorphic.

For example:

* Node names differ, but the hierarchy is the same
* Directory names differ, but the directory nesting relationships are the same
* Organization members differ, but the organizational hierarchy shape is the same

`tree_isomorphism` can usually not only determine whether two trees are isomorphic, but also return the node correspondence between the two trees.

**Product Value**

* Quickly determine whether two tree structures are consistent
* Supports tree template matching, structural deduplication, and structural merging
* Can be used to compare whether two hierarchical systems differ only in naming
* Suitable for tree-structured data such as organizational structures, classification systems, and directory structures

**Typical Scenarios**

* Organizational structure consistency validation
* Classification tree template matching
* File directory structure comparison
* BOM hierarchy comparison
* Workflow tree or decision tree structure alignment
* Structural deduplication of multiple tree samples

**Common Output**

* If two trees are isomorphic: return node correspondence
* If two trees are not isomorphic: return an empty match or a mismatch result

**Applicability and Characteristics**

* Graph Type: Tree structures, usually undirected trees
* Output: Node mapping / node correspondence
* Compared with general graph isomorphism, tree isomorphism has more specialized structure and is usually more efficient
* Suitable as a foundational capability for validation, comparison, and merging of tree-structured data

---

## 5. Recommended Usage Guide

### 1. Subgraph Pattern Recognition

* Determine whether the target graph contains a specified structural pattern: `subgraph_is_monomorphic`
* Find risk templates in business graphs: `subgraph_is_monomorphic`
* Check whether a flowchart hits a certain structural fragment: `subgraph_is_monomorphic`

### 2. Tree Structure Comparison

* Determine whether two trees are structurally identical: `tree_isomorphism`
* Compare organizational structures, classification trees, and directory trees: `tree_isomorphism`
* Get node correspondences between two trees: `tree_isomorphism`

### 3. Structural Template Matching

* Whether a graph contains a certain pattern: `subgraph_is_monomorphic`
* Whether a tree conforms to a certain hierarchical template: `tree_isomorphism`

### 4. Structural Deduplication and Merging

* Whether two tree structures differ only in node naming: `tree_isomorphism`
* Whether multiple local graph structures hit the same template: `subgraph_is_monomorphic`

---

## 6. Typical Directly Answerable Questions

* “Does this large graph contain this subgraph pattern?”
* “Does this transaction network contain a certain suspicious structure?”
* “Does this flowchart conform to a specified template?”
* “Do these two trees have the same structure?”
* “Do two organizational structures differ only in node names?”
* “Do two directory trees have the same hierarchical structure?”
* “Is this classification system consistent with the standard classification template?”
* “Can you provide the node correspondence between the two trees?”
* “Does this graph contain a certain local relationship pattern?”
* “Can this structure serve as an instance of a certain template?”

---

## 7. Operator Checklist

| No. | Operator Name | Description |
| ---: | --- | --- |
| 1 | `subgraph_is_monomorphic` | Subgraph Monomorphism Testing |
| 2 | `tree_isomorphism` | Tree Isomorphism Testing |