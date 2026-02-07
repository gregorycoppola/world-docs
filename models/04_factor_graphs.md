# Factor Graphs

## What is a Factor Graph?

A factor graph is an undirected bipartite graph that represents the factorization of a function. It consists of:

1. **Variable nodes**: Represent random variables
2. **Factor nodes**: Represent functions over subsets of variables
3. **Edges**: Connect each factor to the variables in its scope

Factor graphs generalize both Bayesian networks and Markov random fields.

## Formal Definition

A factor graph represents a function that factorizes as: