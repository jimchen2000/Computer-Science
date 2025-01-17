---
Created: [[2022-11-26]]
Aliases: 
Types: Card
Tags: 
- 
---
# Bayesian networks
![[Screen Shot 2022-11-28 at 14.40.04.png]]
$$P(x_1, \dots, x_n)=\prod_{i=1}^nP(x_i|Parents(x_i))$$
$$P(x_i|x_1, \dots, x_n)=P(x_i|Parents(x_i))\prod_{j\in children(x_i)}P(x_j|Parents(x_j))$$
e.g., $P(a, \neg b, \neg c, d)=P(\neg b|a, \neg c)\times P(a)\times P(\neg c|d)\times P(d)$

1. nodes: 
   representing each feature in a domain
2. edges: 
   encoding the influence and conditional independence relationships b/w nodes
3. conditional probability table (**CPT**) for each node: 
   listing the probability distribution of the feature conditioned on the other features  connected by edges

- using a **directed-acyclical-graph**-based representation to encode the structural relationships b/w subsets of features in a domain
- offering a useful compromise b/w model compactness and predictive accuracy
- Computing a conditional probability for a node becomes more complex if the value of one or more of the parent nodes is unknown. → use **summing out** in [[Joint probability distribution]]
e.g., If C is unknown, B depends on D. To compute $P(b|a, d)$, 
1. $P(c|d)=0.2$ and $P(\neg c|d)=0.8$
2. $$P(b|a, C)=\sum_iP(b|a, C_i)=\sum_i\frac{P(b, a, C_i)}{P(a, C_i)}=\frac{P(b|a, c)\cdot P(a)\cdot P(c|d)+P(b|a, \neg c)\cdot P(a)\cdot P(\neg c|d)}{P(a)\cdot P(c|d)+P(a)\cdot P(\neg c|d)}$$

- more compact than full [[Joint probability distribution]]
- not assuming all features are conditional independent like the [[Naive Bayes model]]
- If the topological structure correctly reflects the causal relationships b/w features in a dataset, the Bayesian network is called a **causal graph**. 

## Markov blanket
![[Screen Shot 2022-11-28 at 14.55.50.png]]
- the set of nodes in a graph that makes a node independent of the rest of the graph
- the gray nodes define the Markov blanket of the black node
- the black node is conditionally independent of the white nodes, given the state of the gray nodes

## Learning
- using a **local search algorithm** to search for the network topology and CPT parameters fitting data best
- training will be easier if topology is given
1. A seed network is given. 
2. The network adapts iteratively by adding, removing, or reversing links, accompanied by parameter learning. 
   - based on the **MDL principle** (Occam's razor, [[Minimum description length]])
   - often used metric to avoid overfitting: Akaike information criterion (**AIC**) and Bayesian information criterion (**BIC**)

### Akaike information criterion
$$AIC(N, D)=2\cdot\log P(D|N)-2d=2\cdot\log P(D|G,\Theta)-2d$$
, where $N$ is Bayesian network, 
$D$ is data, 
$d$ is number of parameters, 
$G$ is topology, 
and $\Theta$ is CPT
### Bayesian information criterion
$$BIC(N, D)=2\cdot\log P(D|N)-d\cdot\log n=2\cdot\log P(D|G, \Theta)-d\cdot\log n$$
, where $N$ is Bayesian network, 
$D$ is data, 
$d$ is number of parameters, 
$n$ is $|D|$, 
$G$ is topology, 
and $\Theta$ is CPT
- $\log P(D|G, \Theta)$ can be compted using metrics such as **Bayesian score** or the **K2 score**

## Prediction
$$\mathbf{M}(q)=\arg\max_{l\in levels(t)}BayesianNetwork(t=l, q)$$
- if there're misssing values, use **summing out**!

## Calculation
- The calculation of a probability becomes computationally complex, particularly when we need to sum out one or more features. 
- To resuce complexity of the calculations, [[Dynamic programming]] techniques (**variable elimination algorithm**) can be considered to avoid repeated computations. 
- Alternative method: **Markov chain Monte Carlo** (**MCMC**) algorithms (**Gibbs sampling** is one of the best known)
### Gibbs sampling
- initializing a Bayesian network by 
  1. clamping the values of the evidence nodes (at random/from predefined list)
  2. randomly assigning values to the non-evidence nodes
- iteratively generating samples by changing the value of one of the no-evidence nodes
- **Burn-in time** is to allow the Markov chain to settle into a state that is independent of the initial random state. 
- The time it takes for the Markov chain to forget the initial random state is called the **mixing time**. 
#### Technical requirement
1. The sampling distribution must be a **stationary distribution**. 
2. The Markov chain must be **ergodic**. 
3. The generated state should be **independent** of each other. 
#### Iteration
1. Select one node for update at random or in a predefined order
2. Updated value of the selected node is then drawn from the distribution for that node conditioned on the current values of all the other nodes
