---
sidebar_position: 9
---

# Link Prediction Operator Set

**Operator Category**: Link Prediction (potential relationship prediction and similarity evaluation)

**Algorithm Count**: 11

**Applicable Stages**: Potential edge prediction, relationship recommendation, similar node discovery, hidden relationship mining, intra-community relationship enhancement, cross-community connection analysis, structural similarity evaluation, relationship strength scoring, path- and flow-assisted prediction

**Product Positioning**: Provides a link prediction and relationship scoring capability base for answering "which nodes may connect in the future / which relationships are worth recommending / which nodes are structurally similar / which hidden relationships are stronger / how community structure affects potential connections".

---

## I. Operator Set Overview

The Link Prediction operator set focuses on **predicting, scoring, and ranking potential connections in a network**, mainly addressing the following questions:

1. **Potential Relationship Prediction**
   - Which currently unconnected nodes are more likely to form connections in the future?
   - Which "non-edges" are most worth recommending or paying attention to?
   - Which node pairs have high structural proximity?

2. **Common Neighbors and Local Structure**
   - Do two nodes share enough common neighbors?
   - Are the common neighbors ordinary nodes, or low-degree, high-value, strong-signal nodes?
   - Are high-degree nodes more likely to attract new connections?

3. **Community-Aware Prediction**
   - Are two nodes in the same community?
   - Do common neighbors within the community enhance the likelihood of a connection?
   - Does the potential relationship tend to be intra-community cohesive or inter-community bridging?

4. **Global Similarity and Random Walks**
   - Are two nodes similar in global structure?
   - Can node similarity be estimated through paths, random walks, or sampling methods?
   - Is the method suitable for approximate similarity computation on large graphs?

5. **Relationship Strength and Structural Auxiliary Judgment**
   - Do nodes have strong reachability or flow-carrying capacity?
   - Do common neighbors between two nodes provide strong relationship discrimination?
   - Which potential connections have higher explanatory value?

---

## II. Operator Capability Classification

| Capability Type                    | Corresponding Operator                  | Description                                                              |
| ---------------------------------- | --------------------------------------- | ------------------------------------------------------------------------ |
| Common Neighbor Ratio              | `jaccard_coefficient`                   | Measures node similarity based on intersection over union of neighbor sets |
| Rare Common Neighbor Weighting     | `adamic_adar_index`                     | Rare common neighbors contribute more; discovers high-quality common relationships |
| Resource Allocation Scoring        | `resource_allocation_index`             | Low-degree common neighbors get higher weight; strengthens hidden strong relationships |
| Preferential Attachment Prediction | `preferential_attachment`               | Predicts hub attractiveness based on product of node degrees             |
| Community-Aware Common Neighbors   | `cn_soundarajan_hopcroft`               | Common neighbors within the same community contribute more to potential connections |
| Community-Aware Resource Allocation| `ra_index_soundarajan_hopcroft`         | Combines community constraints with resource allocation idea for scoring |
| Within/Inter-Community Analysis    | `within_inter_cluster`                  | Distinguishes intra-community from inter-community common neighbors      |
| Global Structural Similarity       | `simrank_similarity`                    | Recursive similarity: “similar nodes connect to similar nodes”           |
| Max-Flow Relationship Strength     | `maximum_flow`                          | Evaluates connection capability or relationship strength via max transmittable flow |
| Sampling-Based Path Similarity     | `panther_similarity`                    | Estimates node similarity through random path sampling                   |
| Common Neighbor Dispersion         | `dispersion`                            | Measures how dispersed common neighbors are; identifies more meaningful potential relationships |

---

## III. General Input/Output Conventions

- **Input `G`**: NetworkX Graph / DiGraph
- **Common Input Parameters**:
  - `ebunch`: Set of node pairs for which to compute link prediction scores
  - `community`: Node attribute name representing community label (for community-aware algorithms)
  - `source` / `target`: Source and target nodes (for flow or similarity queries)
  - `capacity`: Edge capacity attribute name (for max-flow related computations)
- **Common Output Types**:
  - Link prediction: `iterator of (u, v, score)`
  - Similarity: similarity score, node similarity dictionary, or matrix
  - Flow: max-flow value and flow distribution structure
  - Scoring: Higher values usually indicate higher likelihood of potential connection, structural similarity, or relationship strength

---

## IV. Detailed Operator Descriptions

### 1. jaccard_coefficient – Jaccard Coefficient

**Function Description**  
Measures the overlap ratio of two nodes' neighbor sets: proportion of common neighbors out of total neighbors.

```
J(u, v) = |N(u) ∩ N(v)| / |N(u) ∪ N(v)|
```

**Product Value**

- Provides a simple, intuitive, interpretable similarity score
- Suitable for measuring relationship overlap between nodes
- Has a dampening effect on high-degree nodes, avoiding over-scoring simply because they have many neighbors

**Typical Scenarios**

- Friend recommendation
- Product similarity recommendation
- Content association recommendation
- User interest similarity analysis
- Potential edge ranking in relationship networks

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Output: `(u, v, score)`
- Features: Emphasizes the ratio of common neighbors rather than absolute count

---

### 2. adamic_adar_index – Adamic-Adar Index

**Function Description**  
Weights common neighbors: the smaller the degree (the rarer the common neighbor), the greater its contribution. Low-degree common neighbors usually represent rarer, more discriminative relationship signals.

```
AA(u, v) = Σ 1 / log(deg(w)), w ∈ CN(u, v)
```

**Product Value**

- Strengthens the predictive value of “rare common neighbors”
- Reflects relationship quality better than raw common neighbor count
- Suitable for discovering potential connections within small circles, low-exposure but strongly correlated relationships

**Typical Scenarios**

- High-quality friend recommendation in social networks
- Hidden intermediary relationship discovery in anti-fraud
- Research collaboration prediction
- Small-circle relationship mining
- Deep matching of user interests

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Output: `(u, v, score)`
- Features: Penalizes high-degree common neighbors, but the penalty is relatively mild

---

### 3. preferential_attachment – Preferential Attachment

**Function Description**  
Based on the “rich get richer” principle: nodes with higher degrees are more likely to form new connections. The score for a pair is usually the product of their degrees.

```
PA(u, v) = deg(u) × deg(v)
```

**Product Value**

- Captures the attraction effect of hub nodes / high-degree nodes
- Does not depend on common neighbors; suitable for link prediction in sparse graphs
- Can measure the probability of potential connections driven by node influence

**Typical Scenarios**

- Follow recommendations on social platforms
- Predicting connections to influencers / popular accounts
- Citation network prediction
- Web link prediction
- Relationship prediction for highly active nodes in transaction networks

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Output: `(u, v, score)`
- Features: Biased toward influence and activity prediction rather than similarity

---

### 4. resource_allocation_index – Resource Allocation Index

**Function Description**  
Assumes common neighbors allocate resources to the two endpoints. The lower the degree of a common neighbor, the more resources it can allocate to the target nodes.

```
RA(u, v) = Σ 1 / deg(w), w ∈ CN(u, v)
```

**Product Value**

- Strengthens the contribution of low-degree common neighbors
- Penalizes high-degree common neighbors more strongly than Adamic-Adar
- Suitable for identifying low-exposure, tightly bound, hidden potential relationships

**Typical Scenarios**

- Hidden relationship discovery
- Fraud ring relationship mining
- Small-scale strong relationship recommendation
- Supply chain implicit association analysis
- Internal collaboration prediction

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Output: `(u, v, score)`
- Features: Focuses more on low-degree common neighbors; suitable for scenarios that emphasize relationship rarity

---

### 5. cn_soundarajan_hopcroft – Community-Aware Common Neighbors

**Function Description**  
Incorporates community information on top of common neighbors. Common neighbors within the same community are considered more important, thereby increasing the score of potential connections within the same community.

**Product Value**

- Combines local structure with community structure
- Strengthens intra-community relationship recommendations
- More friendly for relationship prediction within circles, organizations, interest groups

**Typical Scenarios**

- Intra-department collaboration recommendation
- Friend recommendation within interest communities
- Potential relationship discovery within circles
- Intra-organization relationship enhancement
- Relationship completion in community operations

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Input dependency: Nodes must have community label attributes
- Output: `(u, v, score)`
- Features: Suitable for enhancing intra-community relationships; not directly usable without community labels

---

### 6. ra_index_soundarajan_hopcroft – Community-Aware Resource Allocation Index

**Function Description**  
Combines resource allocation idea with community structure. Low-degree common neighbors already contribute highly; if they also belong to the same community, their predictive value is further strengthened.

**Product Value**

- Considers both common neighbor rarity and community consistency
- Suitable for discovering hidden strong relationships within communities
- More powerful than the basic resource allocation index for networks with layered circles

**Typical Scenarios**

- Potential collaboration discovery within enterprises
- Strong relationship recommendation within communities
- Organizational relationship completion
- Recommendation of interest group members
- Same-fraud-ring relationship mining in risk control networks

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Input dependency: Nodes must have community label attributes
- Output: `(u, v, score)`
- Features: Low-degree common neighbor + same community typically yields highest score

---

### 7. within_inter_cluster – Within/Inter-Community Score

**Function Description**  
Compares intra-community common neighbors and inter-community common neighbors of a node pair, measuring whether a potential connection tends to be cohesive (intra-community) or bridging (inter-community).

**Product Value**

- Distinguishes internally reinforcing relationships from cross-circle bridging relationships
- Suitable for analyzing whether a relation has “circle-breaking” value
- Helps judge the structural role of a potential connection

**Typical Scenarios**

- Cross-department collaboration discovery
- Cross-interest circle recommendation
- Community bridge node analysis
- Organizational network structure optimization
- Diversity recommendation in social networks

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Input dependency: Nodes must have community label attributes
- Output: `(u, v, score)`
- Features: Suitable for scenarios that care about both intra-community cohesion and inter-community connection

---

### 8. simrank_similarity – SimRank Similarity

**Function Description**  
Based on the recursive idea that “two nodes are similar if they are connected to similar nodes”, computes global structural similarity between nodes.

**Product Value**

- Does not rely solely on direct common neighbors; considers deeper structural similarity
- Suitable for discovering nodes with similar structural roles
- Applicable to recommendation, entity matching, graph structure similarity analysis

**Typical Scenarios**

- User similarity analysis
- Webpage structural similarity computation
- Knowledge graph entity similarity
- Similar paper discovery in citation networks
- Structural similarity recall in recommendation systems

**Applicability & Characteristics**

- Graph type: Directed / undirected graphs
- Output: Node similarity scores or similarity result structure
- Note: Typically more computationally expensive than local heuristic link prediction algorithms

---

### 9. maximum_flow – Maximum Flow

**Function Description**  
Computes the maximum flow that can be transmitted from a source node to a target node under capacity constraints. Can be used to measure the connection carrying capacity or relationship strength between two nodes.

**Product Value**

- Evaluates connection strength from the perspective of paths and capacities
- Helps determine whether there is a strong reachability relationship between two nodes
- Suitable for relationship capability analysis in weighted / capacitated networks

**Typical Scenarios**

- Strength analysis of fund flow relationships
- Supply chain path capability assessment
- Traffic and logistics network capacity analysis
- Communication network capacity analysis
- Risk propagation path capability assessment

**Applicability & Characteristics**

- Graph type: Typically directed graphs, or networks with capacity attributes
- Key parameters: `source`, `target`, `capacity`
- Output: Maximum flow value and flow distribution structure
- Features: More focused on relationship strength and path capability assessment, not traditional common-neighbor link prediction

---

### 10. panther_similarity – Panther Similarity

**Function Description**  
Estimates node similarity through random path sampling, suitable for approximate similarity computation on large-scale graphs.

**Product Value**

- Supports similar node discovery on large graphs
- Compared to exact computation, more suitable for approximate recall
- Serves as a candidate node generation capability in recommendation systems

**Typical Scenarios**

- Large-scale user similarity recall
- Content similarity recommendation
- Similar node retrieval in graph databases
- Approximate similar entity discovery in knowledge graphs
- Large-scale relationship network exploration

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Output: Similar nodes or similarity results
- Features: Sampling-based method, suitable for efficiency-first large graph scenarios

---

### 11. dispersion – Dispersion

**Function Description**  
Measures how dispersed the common neighbors of two nodes are. Instead of only counting common neighbors, `dispersion` focuses on whether the common neighbors are poorly connected to each other, thus indicating whether a potential relationship carries more information.

**Product Value**

- Helps identify more meaningful potential connections
- Distinguishes ordinary common-friend relationships from stronger structural signals
- Suitable for discovering non-obvious but noteworthy relationships in social networks

**Typical Scenarios**

- Friend recommendation in social networks
- Hidden strong relationship discovery
- Community boundary relationship identification
- Cross-circle weak tie analysis
- Relationship recommendation quality optimization

**Applicability & Characteristics**

- Graph type: Typically undirected graphs
- Output: Dispersion score for node pairs
- Features: Focuses on the structural distribution among common neighbors, not just their count

---

## V. Recommended Usage Guide

### 1. Fast Link Prediction Based on Common Neighbors

- Common neighbor ratio: `jaccard_coefficient`
- Emphasize rare common neighbors: `adamic_adar_index`
- Stronger penalty for high-degree common neighbors: `resource_allocation_index`
- Focus on hub attractiveness: `preferential_attachment`

### 2. Community-Aware Link Prediction

- Strengthen intra-community common neighbors: `cn_soundarajan_hopcroft`
- Combine community and low-degree common neighbors: `ra_index_soundarajan_hopcroft`
- Distinguish intra-community cohesion vs. inter-community bridging: `within_inter_cluster`

### 3. Global Similarity and Approximate Similarity

- Recursive structural similarity: `simrank_similarity`
- Large-scale approximate similar node discovery: `panther_similarity`

### 4. Relationship Strength and Path Capability Analysis

- Evaluate relationship strength based on capacity and path capability: `maximum_flow`
- Determine whether common neighbor structure is more discriminative: `dispersion`

### 5. Scenario-Based Selection Suggestions

- **Simple, interpretable recommendation scores**: `jaccard_coefficient`
- **Highlighting high-quality common friends**: `adamic_adar_index`
- **Mining hidden, low-exposure strong relationships**: `resource_allocation_index`
- **Predicting hub attraction capability**: `preferential_attachment`
- **Have community labels, do intra-circle recommendation**: `cn_soundarajan_hopcroft`
- **Community + hidden strong relationship mining**: `ra_index_soundarajan_hopcroft`
- **Analyze cohesion vs. cross-circle connections**: `within_inter_cluster`
- **Global structural similarity**: `simrank_similarity`
- **Evaluate relationship strength from flow-carrying perspective**: `maximum_flow`
- **Efficiently find similar nodes in large graphs**: `panther_similarity`
- **Determine whether common neighbor structure indicates stronger relationship signals**: `dispersion`

---

## VI. Typical Questions That Can Be Directly Answered

- “Which users are most likely to form new relationships?”
- “Who am I most likely to know but haven’t met yet?”
- “Which accounts have hidden relationships?”
- “Which products or content have potential strong associations?”
- “Which nodes are structurally similar even though they are not directly connected?”
- “Which relationship signals among common friends are more valuable?”
- “Which potential connections are intra-community reinforcement relationships?”
- “Which potential connections might be inter-community bridges?”
- “Which node pairs have the strongest path or carrying capacity?”
- “Which nodes are most similar in large graphs?”
- “Which common neighbor structures are more dispersed and better indicate potential strong relationships?”

---

## VII. Operator List

| No. | Operator Name                       | Description (Chinese original)        |
| --- | ----------------------------------- | ------------------------------------- |
| 1   | `jaccard_coefficient`               | Jaccard common neighbor similarity coefficient |
| 2   | `adamic_adar_index`                 | Adamic-Adar rare common neighbor weighting index |
| 3   | `preferential_attachment`           | Preferential attachment score         |
| 4   | `resource_allocation_index`         | Resource allocation index             |
| 5   | `cn_soundarajan_hopcroft`           | Community-aware common neighbor index |
| 6   | `ra_index_soundarajan_hopcroft`     | Community-aware resource allocation index |
| 7   | `within_inter_cluster`              | Within/Inter-community common neighbor score |
| 8   | `simrank_similarity`                | SimRank global structural similarity  |
| 9   | `maximum_flow`                      | Maximum flow relationship strength evaluation |
| 10  | `panther_similarity`                | Panther random‑walk path sampling similarity |
| 11  | `dispersion`                        | Common neighbor dispersion score      |
