---
sidebar_position: 21
---

# Graph Neural Networks Operator Set

**Operator Category**: Graph Neural Networks (Graph neural network convolution layers and representation learning operators)

**Algorithm Count**: 25

**Applicable Stages**: Graph representation learning, node classification/regression, link prediction, graph classification, feature extraction and dimensionality reduction, end-to-end graph neural network model construction

**Product Positioning**: Provides a rich and immediately usable library of convolution operators for answering “how to automatically learn low-dimensional vector representations of nodes or graphs through graph convolution, attention, message passing, and related mechanisms, so as to support downstream tasks such as classification, prediction, and clustering.”

---

## 1. Operator Set Overview

The Graph Neural Networks operator set focuses on **message-passing-based graph representation learning layers**, combining traditional graph algorithms with deep learning to mainly answer the following questions:

1. **Node Representation Learning**
   - How can we learn dense vector representations for each node from graph topology and node features?
   - How should convolution kernels be designed to better aggregate neighborhood information?

2. **Feature Transformation and Aggregation**
   - Should neighbors be aggregated by mean, sum, max, or an attention mechanism?
   - Should edge features, gating, or skip connections be introduced to enhance expressiveness?

3. **Higher-order and Multi-scale Structure**
   - How can multi-hop neighborhood information be captured without causing oversmoothing?
   - How can hierarchical representations be achieved using polynomial filters or mixed-scale modules?

4. **Heterogeneous and Complex Graphs**
   - How can heterogeneous graphs with different node/edge types be handled?
   - How can convolution be defined on special structures such as point clouds and hypergraphs?

5. **Model Building and Layer Selection**
   - Which convolution layer should be chosen for which task?
   - How can these operators be combined to build a complete graph neural network?

---

## 2. Operator Capability Classification

| Capability Type | Corresponding Operators | Function Description |
|---|---|---|
| Spectral Graph Convolution | `pyg_gcnconv` | First-order Chebyshev-style spectral convolution that aggregates neighbors uniformly |
| Spatial General Aggregation | `pyg_sageconv` | Samples and aggregates neighbors using mean/LSTM/pooling and similar strategies |
| Graph Attention Mechanism | `pyg_gatconv`, `pyg_gatv2conv`, `pyg_supergatconv` | Assigns learnable attention weights to neighbors and supports multi-head mechanisms |
| Transformer-style | `pyg_transformerconv` | Combines self-attention with edge features and can incorporate positional encoding |
| Residual Gated Convolution | `pyg_resgatedgraphconv` | Graph convolution with residual connections and gating mechanisms |
| Edge-feature Fusion Convolution | `pyg_egconv`, `pyg_genconv`, `pyg_filmconv` | Incorporates edge features into message passing for feature modulation or deep aggregation |
| General / Heterogeneous Convolution | `pyg_generalconv`, `pyg_heteroconv` | The former provides configurable aggregation/message functions; the latter supports multi-relation convolution on heterogeneous graphs |
| Point-cloud and Geometric Convolution | `pyg_gravnetconv`, `pyg_pointtransformerconv`, `pyg_pdnconv` | Convolution designed for point clouds, physical space, or geometric features |
| Higher-order Polynomial Convolution | `pyg_armaconv`, `pyg_sgconv`, `pyg_tagconv` | Uses higher-order neighborhood polynomial expansion to enlarge receptive fields and mitigate oversmoothing |
| Multi-scale Hybrid Convolution | `pyg_mixhopconv` | Mixes features from neighbors of different orders to capture multi-granularity structure |
| Oversmoothing Suppression | `pyg_ssgconv` | Uses structure-aware gating to suppress irrelevant information propagation |
| Clustering / Coarsening Convolution | `pyg_clustergcnconv` | Performs convolution based on graph clustering and is suitable for large-scale graphs |
| Hypergraph Convolution | `pyg_hypergraphconv` | Defines convolution on hypergraph structures to support higher-order relations |
| Local Extremum Convolution | `pyg_leconv` | Selects extremum features from local neighbors to enhance structural sensitivity |
| Multi-frequency Convolution | `pyg_mfconv` | Uses multi-frequency filters to capture structural information across different frequency bands |
| Feature-similarity Convolution | `pyg_feastconv` | Dynamically aggregates neighbors based on feature similarity and is suitable for heterophilous graphs |

---

## 3. General Input and Output Conventions

- **Input `G`**: Usually a PyTorch Geometric (PyG) `Data` object containing:
  - `x`: node feature tensor with shape `[N, in_channels]`
  - `edge_index`: edge index tensor with shape `[2, E]` (COO format)
  - `edge_attr`: (optional) edge feature tensor with shape `[E, edge_dim]`
- **Common Output Types**:
  - Updated node feature tensor `[N, out_channels]`
  - Some operators return intermediate attention weights or gate values (for example, attention layers)
- **Hyperparameters**:
  - `in_channels` / `out_channels`: input/output feature dimensions
  - `heads`: number of attention heads (attention-based layers)
  - `K`: polynomial order or hop count (higher-order convolution)
  - `dropout` / `bias` / `improved` and others

---

## 4. Detailed Operator Descriptions

### 1. pyg_gcnconv —— Graph Convolutional Network Layer

**Function Description**  
Implements the classic spectral graph convolution (GCN) by performing a first-order approximation of the normalized adjacency matrix and aggregating neighbor features uniformly.

**Product Value**
- The most fundamental graph convolution unit; easy to use and stable, suitable as a model baseline
- Provides an entry-level solution for node representation learning
- Performs well in tasks such as semi-supervised node classification

**Typical Scenarios**
- Node classification in academic citation networks
- User attribute prediction in social networks
- Entity classification in knowledge graphs

**Applicability and Characteristics**
- Graph Type: Undirected / directed homogeneous graphs
- Aggregation Method: Normalized summation
- Can add self-loops and supports unweighted edges

---

### 2. pyg_sageconv —— Graph Sample-and-Aggregate Layer

**Function Description**  
A GraphSAGE layer that samples neighbors and uses a specified aggregation function such as mean, LSTM, or pooling to generate the representation of the target node.

**Product Value**
- Supports inductive learning and can generalize to unseen nodes
- Scales to large graphs through sampling mechanisms
- Flexible aggregation functions adapt to different data characteristics

**Typical Scenarios**
- User/item embedding in large-scale recommendation systems
- Fast representation of new nodes in dynamic graphs
- Protein function prediction

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Main Parameters: `aggr` (aggregation method), `normalize`
- Fixed-size neighbor sampling can be specified during computation

---

### 3. pyg_gatconv —— Graph Attention Network Layer

**Function Description**  
A graph attention layer (GAT) that assigns different weights to different neighbors through a self-attention mechanism and performs weighted aggregation.

**Product Value**
- Captures different importance levels of neighbors and improves model discriminative power
- Multi-head attention can stabilize training and improve robustness
- Does not require prior knowledge of the global graph structure

**Typical Scenarios**
- Important paper identification in citation networks
- Molecular property prediction
- Traffic-flow node influence assessment

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameters: `heads` (number of heads), `concat`, `dropout`
- Supports multi-head concatenation or averaging

---

### 4. pyg_gatv2conv —— Graph Attention Network Layer V2

**Function Description**  
An improved GATv2 layer that adopts a dynamic attention computation order, making the ranking ability of attention weights more expressive.

**Product Value**
- Solves the issue of limited attention expressiveness of standard GAT on certain graphs
- Can effectively model heterophilous edge structures
- Provides stronger theoretical robustness

**Typical Scenarios**
- Node classification on heterophilous graphs (such as web links)
- Scenarios with large graph-structure noise
- Tasks requiring fine-grained modeling of edge differences

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Similar parameters to GATConv, with a more general attention computation scheme

---

### 5. pyg_transformerconv —— Graph Transformer Convolution Layer

**Function Description**  
Introduces the Transformer self-attention mechanism into graphs, using neighboring nodes as key-value pairs and supporting edge features as positional encoding.

**Product Value**
- Suitable for long-range dependencies and cases where edge attributes should modulate attention
- Combines Transformer expressiveness with graph-structural inductive bias
- Can be seamlessly connected with large-model pretraining paradigms

**Typical Scenarios**
- Molecular graph property prediction (where edges are chemical bonds)
- Joint modeling of text graphs
- Knowledge graph link prediction

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs, supports `edge_attr`
- Parameters: `heads`, `dropout`, `edge_dim`

---

### 6. pyg_resgatedgraphconv —— Residual Gated Graph Convolution Layer

**Function Description**  
A graph convolution layer with residual connections and gating mechanisms, using gate signals to regulate information retention and forgetting, which is beneficial for training deep graph networks.

**Product Value**
- Effectively alleviates oversmoothing and vanishing gradients in deep graph networks
- Gating mechanisms provide adaptive information-flow control
- Suitable for deep architectures requiring dozens of convolution layers

**Typical Scenarios**
- Deep feature extraction on large-scale graphs
- Molecular generation requiring complex nonlinear transformations
- Deep social network analysis

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Core Structure: Residual + gating, similar to GRU

---

### 7. pyg_gravnetconv —— GravNet Convolution Layer

**Function Description**  
A convolution layer inspired by physical gravity, which weights message passing by computing “gravitational attraction” between node pairs, often used in point-cloud processing.

**Product Value**
- Suitable for geometric data where spatial distance matters
- Does not require fixed topology; dynamically constructs edges based on distances in feature space
- Adapts well to irregular point clouds

**Typical Scenarios**
- High-energy physics particle jet classification
- 3D point-cloud segmentation
- Particle systems in physical simulation

**Applicability and Characteristics**
- Graph Type: Geometric structures on dynamic graphs
- Parameters: spatial dimensionality, distance metric

---

### 8. pyg_supergatconv —— SuperGAT Convolution Layer

**Function Description**  
Introduces self-supervised signals into graph attention and combines edge prediction tasks so that attention weights can simultaneously perform neighborhood aggregation and edge-existence prediction.

**Product Value**
- Improves the quality and interpretability of attention weights through multitask learning
- Has some ability to distinguish noisy edges
- Performs strongly on link prediction tasks

**Typical Scenarios**
- Joint training of edge prediction and node classification
- Learning on relation graphs with noisy edges
- Friend recommendation in social networks

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Supports `att_dropout` and `is_super_gat` modes

---

### 9. pyg_egconv —— Edge-conditioned Convolution Layer

**Function Description**  
A convolution layer that efficiently uses edge features by dynamically computing message transformation matrices or weights from edge features, enabling edge-attribute-driven aggregation.

**Product Value**
- Enriches the use of edge features such as relation type and strength
- Essential in molecular graphs (bond types) and physical interactions
- Can learn interactions over edge features to improve model flexibility

**Typical Scenarios**
- Molecular fingerprint prediction
- Traffic speed prediction in road networks (where edges represent road properties)
- Modeling rating edges in recommendation systems

**Applicability and Characteristics**
- Graph Type: Graphs with edge features
- Parameter: `nn` as the edge-feature transformation network

---

### 10. pyg_pdnconv —— Path-defined Network Convolution Layer

**Function Description**  
A convolution based on path-defined networks that aggregates information through combined transformations of multiple transition matrices and is suitable for modeling multi-step relations.

**Product Value**
- Explicitly encodes path information beyond one-hop neighbors
- Can be used to control the distance and direction of information propagation
- Especially useful in relation-rich data such as knowledge graphs

**Typical Scenarios**
- Multi-hop reasoning in knowledge graphs
- Multi-step reachability analysis in transportation systems
- Biological pathway modeling

**Applicability and Characteristics**
- Graph Type: Directed / undirected graphs
- Parameter: edge-type matrices

---

### 11. pyg_generalconv —— General Graph Convolution Layer

**Function Description**  
A highly configurable graph convolution layer that allows users to customize the message function, aggregation function, and update function, providing a flexible convolution framework.

**Product Value**
- Enables rapid experimentation with new convolution variants without implementing message passing from scratch
- A useful tool for teaching and prototyping
- Covers many existing GNN layers by combining different functions

**Typical Scenarios**
- GNN algorithm research
- Custom message-passing rules
- Fast testing of new aggregation methods

**Applicability and Characteristics**
- Graph Type: Arbitrary, depending on user configuration
- Parameters: `in_channels`, `out_channels`, `aggr`, `message_func`, and others

---

### 12. pyg_hypergraphconv —— Hypergraph Convolution Layer

**Function Description**  
Defines convolution on hypergraphs, where hyperedges can connect multiple nodes, for modeling higher-order group relationships.

**Product Value**
- Breaks through the limitations of traditional binary edges and directly models multi-party interactions such as groups or meetings
- Preserves Laplacian smoothing properties under hypergraph structures
- Suitable for cooperative networks, recommendation systems, and similar scenarios

**Typical Scenarios**
- Co-authorship networks (author-paper)
- Conference participation networks
- Group recommendation and bundled-product recommendation

**Applicability and Characteristics**
- Graph Type: Hypergraphs, requiring `hyperedge_index`
- Parameter: optional attention through `use_attention`

---

### 13. pyg_leconv —— Local Extremum Convolution Layer

**Function Description**  
A convolution layer that captures structural change boundaries by selecting extremum features such as local maxima or minima from neighborhood features.

**Product Value**
- Sensitive to signal changes such as image gradients and point-cloud boundaries
- Good at discovering local structural mutations, improving tasks such as segmentation
- Lightweight computation with no parameter learning

**Typical Scenarios**
- Boundary detection in mesh / point-cloud segmentation
- Anomaly detection in sensor networks
- Shape analysis

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Operation: local extremum aggregation, usually without learnable weights

---

### 14. pyg_clustergcnconv —— Cluster GCN Convolution Layer

**Function Description**  
A convolution layer based on graph clustering algorithms: the graph is first partitioned into clusters, then convolution is performed within clusters, significantly reducing computation and memory cost on large-scale graphs.

**Product Value**
- Allows deep learning to scale to million-level or even larger graphs
- Preserves locally dense connectivity through graph clustering, improving computational efficiency
- Can be stacked with other layers to build deep large-scale GNNs

**Typical Scenarios**
- Node classification on ultra-large social networks
- E-commerce graph recommendation
- Full-database analysis of citation networks

**Applicability and Characteristics**
- Graph Type: Large-scale homogeneous graphs
- Requires support from clustering partition algorithms, usually combined with `ClusterData`

---

### 15. pyg_genconv —— Generalized Edge Convolution Layer

**Function Description**  
A deep convolution layer used in Deep Graph Convolutional Generative Networks, supporting multiple message-construction and normalization methods and excelling at building deep generative models.

**Product Value**
- Suitable for graph generation tasks and widely used in deep learning tasks such as molecular generation
- Supports deep-network stabilization techniques such as residual connections and batch normalization
- Allows more than 10 layers and even deeper architectures

**Typical Scenarios**
- Molecular graph generation
- Graph autoencoders
- Graph structure reconstruction

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs, supports edge features
- Parameters: `aggr`, `msg_norm`, `learn_msg_scale`, and others

---

### 16. pyg_filmconv —— FiLM Convolution Layer

**Function Description**  
Uses Feature-wise Linear Modulation (FiLM) to generate transformation parameters from the center node and neighbor features, scaling and shifting messages accordingly.

**Product Value**
- Provides more flexible node-level transformation than ordinary attention and gating
- Particularly suitable for knowledge graphs with rich relation types
- Performs strongly on relation prediction tasks

**Typical Scenarios**
- Knowledge graph completion
- Multi-relation social networks
- Node classification in heterogeneous information networks

**Applicability and Characteristics**
- Graph Type: Supports relation types (`edge_type`)
- Parameter: `num_relations`, with modulation generated by a neural network

---

### 17. pyg_mfconv —— Multi-frequency Convolution Layer

**Function Description**  
Uses multiple frequency filters to aggregate information from different spectral perspectives, preserving both low-frequency and high-frequency signals and alleviating oversmoothing.

**Product Value**
- Breaks through the limitation of traditional GNNs that mainly preserve low-frequency signals
- Performs excellently on heterophilous graphs
- Multi-frequency information can complement each other and improve representation richness

**Typical Scenarios**
- Fraud detection (where anomalous signals are often high-frequency)
- Webpage classification (heterophilous links)
- Network alignment

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameters: `max_degree`, `num_filters`

---

### 18. pyg_feastconv —— Feature-steered Attention Convolution Layer

**Function Description**  
Dynamically chooses which neighbors to aggregate based on feature similarity rather than fixed topology-defined neighborhoods, making it suitable for scenarios where feature space determines connection importance.

**Product Value**
- Robust in scenarios where node features dominate and graph structure is inaccurate
- Can be viewed as “soft” attention-based neighborhood selection
- Suitable for point-cloud and vision graph tasks

**Typical Scenarios**
- 3D point-cloud classification and segmentation
- Superpixel graph modeling in images
- Feature-driven community discovery

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs, with `heads` attention heads
- Core Idea: neighborhood reconstruction based on feature-attention coefficients

---

### 19. pyg_pointtransformerconv —— Point Transformer Convolution Layer

**Function Description**  
A Transformer convolution designed specifically for point clouds, integrating positional encoding and feature transformation for unordered point sets.

**Product Value**
- Combines the global receptive field of Transformers with local point-cloud geometry
- Achieves state-of-the-art performance on many 3D understanding tasks
- Supports vector attention and preserves directional information

**Typical Scenarios**
- LiDAR point-cloud segmentation for autonomous driving
- Indoor scene understanding
- 3D object classification

**Applicability and Characteristics**
- Graph Type: Graphs built from point clouds (with positions)
- Parameters: `pos_nn`, `attn_nn`, and others

---

### 20. pyg_heteroconv —— Heterogeneous Graph Convolution Wrapper

**Function Description**  
A wrapper that applies different convolution layers to different relation types in heterogeneous graphs and supports defining convolution operations separately for each edge type.

**Product Value**
- Naturally handles heterogeneous graphs with multiple node and edge types
- Can flexibly combine any existing convolution layers
- The most general component for implementing heterogeneous graph neural networks

**Typical Scenarios**
- Academic networks (author-paper-conference)
- Movie recommendation (user-movie-actor)
- Multimodal knowledge graphs

**Applicability and Characteristics**
- Graph Type: Heterogeneous graphs (`HeteroData`)
- Parameter: a dictionary mapping edge types to convolution modules

---

### 21. pyg_mixhopconv —— MixHop Convolution Layer

**Function Description**  
Captures multi-scale graph structure by mixing representations from neighbors of different orders (different powers of the adjacency matrix).

**Product Value**
- Obtains higher-order information without explicit multi-hop neighbor sampling
- Mitigates oversmoothing by mixing low-order and high-order features to preserve discrimination
- Simple, effective, and computationally efficient

**Typical Scenarios**
- Node classification requiring multi-hop context
- Multi-order influence modeling in social networks
- Graph-level property prediction

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameter: `powers`, specifying the list of adjacency-matrix powers to mix

---

### 22. pyg_ssgconv —— Self-gated Simplified Convolution Layer

**Function Description**  
A variant of Simple Spectral Graph Convolution that suppresses noisy signal propagation through a structure-aware gating mechanism.

**Product Value**
- Adds gating to simplified SGC, improving flexibility
- Suppresses the impact of irrelevant or erroneous edges
- Uses few parameters and runs efficiently, making it suitable for baseline enhancement

**Typical Scenarios**
- Graph learning with noisy edges
- Fast node classification
- Baselines for ablation studies

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameters: `alpha`, `K` hop count

---

### 23. pyg_armaconv —— ARMA Convolution Layer

**Function Description**  
An autoregressive moving average graph convolution that approximates arbitrary rational spectral filters by stacking multiple parallel ARMA filters, improving noise robustness.

**Product Value**
- Captures complex frequency-domain responses better than traditional polynomial convolution
- Allows stacking multiple filters to improve robustness
- More friendly to heterophilous graphs

**Typical Scenarios**
- Node classification in heterophilous networks
- Noise-robust graph representation learning
- Anomaly detection in financial networks

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameters: `num_stacks`, `num_layers`, `dropout`

---

### 24. pyg_sgconv —— Simplified Graph Convolution Layer

**Function Description**  
A simplified convolution layer (SGC) that removes intermediate nonlinear transformations and makes multi-layer graph convolution equivalent to applying the `K`-th power of the adjacency matrix followed by a linear transformation.

**Product Value**
- Extremely efficient and can be viewed as logistic regression over diffused features
- Performs well in small-sample or low-label scenarios
- Easy to interpret and deploy

**Typical Scenarios**
- Fast baseline models
- Resource-constrained environments (edge side)
- Precomputation of feature diffusion

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameter: `K` propagation steps
- No intermediate activation; diffused features can be precomputed

---

### 25. pyg_tagconv —— Topology Adaptive Graph Convolution Layer

**Function Description**  
Topology Adaptive Graph Convolution uses a weighted sum of adjacency matrices of different orders to aggregate neighbors, with weights controlled by learnable coefficients.

**Product Value**
- Adaptively determines the importance of neighbors at different hop distances
- Maintains linear complexity while achieving multi-hop receptive fields
- Has higher capacity than GCN and is suitable for structurally complex graphs

**Typical Scenarios**
- Node classification requiring adaptive receptive fields
- Datasets with highly variable graph structures
- Pooling backbone layers for graph classification

**Applicability and Characteristics**
- Graph Type: Homogeneous graphs
- Parameters: `K` maximum hop count, learnable aggregation weights `alpha`

---

## 5. Recommended Usage Guide

### 1. Quick Start and Baselines

- Simplest spectral convolution: `pyg_gcnconv` or `pyg_sgconv`
- First choice for inductive tasks: `pyg_sageconv`

### 2. Heterogeneous and Multi-relation Graphs

- General heterogeneous graph solution: `pyg_heteroconv` with base convolutions embedded for each edge type
- Relation-rich graphs: `pyg_filmconv` or `pyg_generalconv` combined with edge types

### 3. Deep Networks and Oversmoothing Suppression

- Use residual gating: `pyg_resgatedgraphconv`
- Multi-frequency / mixed-order methods: `pyg_mfconv`, `pyg_mixhopconv`, `pyg_armaconv`
- Adaptive weighted propagation: `pyg_tagconv`

### 4. Attention and Edge Features

- Standard attention: `pyg_gatconv` / `pyg_gatv2conv`
- Edge-feature integration: `pyg_egconv`, `pyg_genconv`, `pyg_transformerconv`
- Self-supervised enhancement: `pyg_supergatconv`

### 5. Geometry and Point Clouds

- Point-cloud specific: `pyg_pointtransformerconv`, `pyg_gravnetconv`
- Dynamically edge-defined: `pyg_feastconv`, `pyg_leconv`

---

## 6. Typical Directly Answerable Questions

- “Which convolution layer is suitable for node classification on my citation network?”
- “How can I define a graph neural network on a heterogeneous graph such as user-product-brand?”
- “In scenarios where edge features are very important, such as relation type or strength, which convolution should I choose?”
- “How can deep graph networks avoid oversmoothing?”
- “Which layer should I use to apply GNNs on point clouds?”
- “Is there a simplified version of GCN for quickly building a baseline?”
- “How should I choose convolution operators for training on large-scale graphs?”
- “Can Transformer-style graph convolution layers handle edge features?”
- “What is the difference between GAT and GATv2 attention layers, and when should I upgrade?”
- “How can I combine the strengths of different convolutions in one model?”

---

## 7. Operator Checklist

| No. | Operator Name | English Description |
|---:|---|---|
| 1 | `pyg_gcnconv` | Standard graph convolutional network layer (first-order spectral approximation) |
| 2 | `pyg_sageconv` | Sample-and-aggregate graph convolution layer (GraphSAGE) |
| 3 | `pyg_gatconv` | Graph attention network layer (GAT) |
| 4 | `pyg_gatv2conv` | Improved graph attention layer (GATv2) |
| 5 | `pyg_transformerconv` | Graph Transformer convolution layer |
| 6 | `pyg_resgatedgraphconv` | Residual gated graph convolution layer |
| 7 | `pyg_gravnetconv` | GravNet convolution layer (point cloud) |
| 8 | `pyg_supergatconv` | Self-supervised enhanced attention graph convolution |
| 9 | `pyg_egconv` | Edge-conditioned convolution layer |
| 10 | `pyg_pdnconv` | Path-defined network convolution layer |
| 11 | `pyg_generalconv` | General configurable graph convolution layer |
| 12 | `pyg_hypergraphconv` | Hypergraph convolution layer |
| 13 | `pyg_leconv` | Local extremum convolution layer |
| 14 | `pyg_clustergcnconv` | Cluster GCN convolution layer (large-scale) |
| 15 | `pyg_genconv` | Generalized deep edge convolution layer (generative models) |
| 16 | `pyg_filmconv` | Feature-wise linear modulation convolution layer (relation modeling) |
| 17 | `pyg_mfconv` | Multi-frequency convolution layer (preferred for heterophilous graphs) |
| 18 | `pyg_feastconv` | Feature-similarity attention convolution layer |
| 19 | `pyg_pointtransformerconv` | Point-cloud Transformer convolution layer |
| 20 | `pyg_heteroconv` | Heterogeneous graph convolution wrapper |
| 21 | `pyg_mixhopconv` | Mixed-order convolution layer |
| 22 | `pyg_ssgconv` | Self-gated simplified graph convolution layer |
| 23 | `pyg_armaconv` | ARMA graph convolution layer (adaptive filtering) |
| 24 | `pyg_sgconv` | Simplified graph convolution layer (SGC) |
| 25 | `pyg_tagconv` | Topology adaptive graph convolution layer (TAG) |