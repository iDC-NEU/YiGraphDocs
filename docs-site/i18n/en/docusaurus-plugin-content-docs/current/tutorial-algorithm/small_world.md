---
sidebar_position: 19
---

# Small-world Operator Set

**Operator Category**: Small-world (Graph rewiring and reference graph generation for small-world network analysis)

**Algorithm Count**: 1

**Applicable Stages**: Small-world property evaluation, randomized network baseline construction, rewiring-based comparative analysis

**Product Positioning**: Provides reference graph generation and random rewiring operators for answering "does this network have small-world characteristics?" and "how does it differ from a random network with a matching degree sequence?"

---

## 1. Operator Set Overview

The Small-world operator set focuses on **reference graph generation and randomized graph rewiring in small-world network analysis**, mainly answering the following questions:

1. **Baseline Construction**
   - How can we generate a reference graph that has the same number of nodes and degree sequence as the original network, but with completely random connection patterns?
   - How can we build a random graph baseline for computing small-world coefficients?

2. **Random Rewiring**
   - Can the network be randomly rewired while keeping the degree sequence unchanged?
   - Are multiedges or self-loops introduced during rewiring, and how can they be avoided?

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operator | Function Description |
|---|---|---|
| Random Reference Graph Generation | `random_reference` | Generate a randomized reference graph through an edge-swap strategy while preserving the degree sequence, for small-world network analysis and null-model comparison |

---

## 3. General Input and Output Conventions

- **Input `G`**: NetworkX undirected graph (usually required to be connected and without self-loops or multiedges)
- **Key Parameters**:
  - `niter`: Number of edge-swap iterations (default is `10 * E`, ensuring sufficient mixing)
  - `connectivity`: Whether the output graph is required to remain connected (default `True`)
- **Common Output Type**: NetworkX Graph (a randomized reference graph with the same node set and degree sequence as the input graph)

---

## 4. Detailed Operator Descriptions

### 1. random_reference —— Generate a Random Reference Graph

**Function Description**  
Randomly rewire the graph through double-edge swaps while strictly preserving the degree of every node, generating a randomized reference graph independent of the original network structure. This reference graph is used to compare metrics such as clustering coefficient and average shortest path length with the original network, thereby determining whether the network has small-world characteristics such as high clustering and short path lengths.

**Product Value**
- Provides a statistical baseline (null model) for small-world network assessment
- Supports hypothesis testing of network randomness
- Can be used in the computation of small-world coefficients such as σ or ω
- Preserves the degree sequence, making comparisons fairer

**Typical Scenarios**
- Validation of the small-world effect in social networks
- Analysis of small-world properties in brain functional connectivity networks
- Randomized controls for protein interaction networks
- Structural randomness assessment of transportation or power networks
- Random network generation for significance testing of network motifs

**Key Parameters**
- `niter`: Number of edge-swap iterations, as an integer. Using the default value is recommended to ensure sufficient mixing
- `connectivity`: Boolean. When `True`, the output graph is guaranteed to remain connected (default behavior); recommended when the original graph is connected

**Applicability and Characteristics**
- Graph Type: Undirected graphs (connected graphs are preferred)
- Output: A random graph with exactly the same degree sequence as `G`
- Algorithmic Nature: Markov-chain edge swapping while preserving the degree sequence
- Complexity: `O(niter * d)`, where `d` is the average degree; overall complexity is usually linearly related to the number of edges

**Notes**
- If the original graph is too small or structurally too special, it may be difficult to achieve sufficient randomization while preserving connectivity
- This operator generates one sample from a **configuration-model-style ensemble matching the degree sequence**, and does not handle weights or directions (directed/weighted versions require other tools)
- Too few iterations may lead to insufficient randomization; using the default value is recommended

---

## 5. Recommended Usage Guide

### 1. Small-world Property Analysis

- Compute the average clustering coefficient `C` and average shortest path length `L` of the original graph
- Call `random_reference` to generate multiple random reference graphs, and compute their average clustering coefficient `C_r` and average shortest path length `L_r`
- If `C >> C_r` and `L ≈ L_r`, then the network exhibits strong small-world characteristics

### 2. Network Motif or Functional Significance Testing

- Generate a large number of `random_reference` samples as a null distribution for testing the statistical significance of certain network properties

---

## 6. Typical Directly Answerable Questions

- “What does a random network look like that matches the given degree sequence?”
- “How can I generate a control network with the same number of nodes and neighbors per node as the original network, but with completely random connections?”
- “Which operator should I use to determine whether my network is a small-world network?”
- “When generating a random baseline graph, is connectivity still guaranteed?”

---

## 7. Operator Checklist

| No. | Operator Name | Description |
|---:|---|---|
| 1 | `random_reference` | Generate a random reference graph with the same node set and degree sequence as the given graph while preserving connectivity |