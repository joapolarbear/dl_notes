# Graph Neural Network (GNN)

## Graph

**Definition**: A Graph contains two components: vertices and edges. A graph *G* can be well described by the set of vertices *V* and edges *E* it contains $G=(V, E)$

## Graph Neural Network

**Definition**: Graph Neural Network is a type of Neural Network which directly operates on the Graph structure.

### Original GNN

**One Example**: node classification problem: each node $v$ is characterized by its feature $x_v$ and associated with a ground-truth label *$t_v$.* 

**Goal:** Given a partially labeled graph *$G$*,  leverage these labeled nodes to predict the labels of the unlabeled,r.

#### Step 1: embedding

Represent each node with a $d-$dimensional vector (state) $h_v$ which contains the information of its neighborhood. Specifically, 
$$
\mathbf{h}_{v}=f\left(\mathbf{x}_{v}, \mathbf{x}_{c o[v]}, \mathbf{h}_{n e[v]}, \mathbf{x}_{n e[v]}\right)
$$

* *$x_{co}[v]$* denotes the features of the edges connecting with *$v$*
* *$h_{ne}[v]$* denotes the embedding of the neighboring nodes of *$v$*
* *$x_ne[v]$* denotes the features of the neighboring nodes of *$v$.* 

Apply Banach [fixed point theorem](https://en.wikipedia.org/wiki/Fixed-point_theorem) and rewrite the above equation as an iteratively update process. Such operation is often referred to as **message passing** or **neighborhood aggregation**.
$$
\mathbf{H}^{t+1}=F\left(\mathbf{H}^{t}, \mathbf{X}\right)
$$
$H$ and $X$ denote the concatenation of all the $h$ and $x$, respectively.

#### Step 2: output

Using the embedding and features, get the output for each node. Both $f$ and $g$ here can be interpreted as <u>feed-forward fully-connected Neural Networks</u>.
$$
\mathbf{O}_{v}=g\left(\mathbf{h}_{v}, \mathbf{x}_{v}\right)
$$

#### Step 3: loss

$$
loss = \sum_{i=1}^p(t_i - o_i)
$$

---

## Several Variants of GNN

### DeepWalk

* The first algorithm proposing node embedding learned in an unsupervised manner
* Resembles <u>word embedding</u> in terms of the training process
* Two steps to learn the embedding
  1. Perform random walks on nodes in a graph to generate node sequences.
  2. Run skip-gram to learn the embedding of each node based on the node sequences generated in step 1.
* **Cons**: not suitable for dynamic graphs since it has to re-train the model whenever a new node comes in

#### GraphSage

* Enables representable embedding to be generated for unseen nodes by aggregating its nearby nodes.
* Allows node embedding to be applied to domains involving **dynamic graph**.

