# Bayesian Networks

## What is a Bayesian Network?

A Bayesian Network (BN) is a directed acyclic graph (DAG) that represents a joint probability distribution over a set of random variables. Each node represents a variable, and edges represent direct probabilistic dependencies.

The key property: a node is conditionally independent of its non-descendants given its parents.

## Formal Definition

A Bayesian Network consists of:

1. **A DAG** G = (V, E) where V is a set of nodes (variables) and E is a set of directed edges
2. **A set of conditional probability distributions** P(Xᵢ | Parents(Xᵢ)) for each node

The joint distribution factorizes as: