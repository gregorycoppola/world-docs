# Bayesian Networks

## What is a Bayesian Network?

A Bayesian Network (BN) is a directed acyclic graph (DAG) that represents a joint probability distribution over a set of random variables. Each node represents a variable, and edges represent direct probabilistic dependencies.

The key property: a node is conditionally independent of its non-descendants given its parents.

## Formal Definition

A Bayesian Network consists of:

1. **A DAG** G = (V, E) where V is a set of nodes (variables) and E is a set of directed edges
2. **A set of conditional probability distributions** P(Xᵢ | Parents(Xᵢ)) for each node

The joint distribution factorizes as:

    P(X₁, X₂, ..., Xₙ) = ∏ᵢ P(Xᵢ | Parents(Xᵢ))

This factorization is what makes Bayesian networks computationally tractable for many problems.

## Example: The Classic "Alarm" Network

Structure:

    Burglary    Earthquake
        \         /
         \       /
          v     v
           Alarm
          /     \
         v       v
     JohnCalls  MaryCalls

Conditional probability tables:

    P(Burglary) = 0.001
    P(Earthquake) = 0.002
    
    P(Alarm | Burglary, Earthquake):
      B=T, E=T: 0.95
      B=T, E=F: 0.94
      B=F, E=T: 0.29
      B=F, E=F: 0.001
    
    P(JohnCalls | Alarm):
      A=T: 0.90
      A=F: 0.05
    
    P(MaryCalls | Alarm):
      A=T: 0.70
      A=F: 0.01

## Independence and D-Separation

The graph structure encodes conditional independence relationships. A variable X is independent of Y given Z if Z "d-separates" X and Y in the graph.

D-separation rules:

1. **Chain**: A → B → C: A ⊥ C | B
2. **Fork**: A ← B → C: A ⊥ C | B
3. **Collider**: A → B ← C: A ⊥ C, but A ⊬ C | B (explaining away)

## Inference Tasks

**Marginal inference**: Compute P(X) by summing out other variables

    P(X) = Σ_{Y,Z,...} P(X, Y, Z, ...)

**Conditional inference**: Compute P(X | E = e) given evidence

    P(X | E = e) = P(X, E = e) / P(E = e)

**Most probable explanation (MPE)**: Find the most likely assignment

    argmax_{x₁,...,xₙ} P(X₁ = x₁, ..., Xₙ = xₙ | E = e)

## Complexity

- Exact inference in general Bayesian networks is **NP-hard**
- Even approximate inference is **NP-hard** in the worst case
- But for special structures (trees, bounded treewidth), polynomial algorithms exist

## Learning

**Parameter learning**: Given structure, learn CPTs from data
- Maximum likelihood: count frequencies
- Bayesian: use Dirichlet priors

**Structure learning**: Learn the DAG from data
- Score-based: search over structures, score by BIC/MDL
- Constraint-based: use independence tests to infer edges
- This is harder and often requires domain knowledge

## Historical Context

- **Pearl (1988)**: Introduced Bayesian networks and belief propagation
- **Lauritzen & Spiegelhalter (1988)**: Junction tree algorithm for exact inference
- **Heckerman (1995)**: Tutorial on learning Bayesian networks

## Relevance to QBBN

QBBN is a Bayesian network with special structure:

1. **Boolean variables only**: Each node is true/false
2. **Bipartite structure**: Alternating AND and OR factors
3. **Logical interpretation**: Nodes represent propositions, edges represent implications

The factorization follows the same principle:

    P(p₁, ..., pₙ, g₁, ..., gₘ) = ∏ᵢ P(gᵢ | parents(gᵢ)) × ∏ⱼ P(pⱼ | parents(pⱼ))

Where AND factors (for groups) and OR factors (for propositions) define the conditional distributions.

## Key References

- Pearl, J. (1988). *Probabilistic Reasoning in Intelligent Systems*
- Neapolitan, R. E. (2003). *Learning Bayesian Networks*
- Koller, D. & Friedman, N. (2009). *Probabilistic Graphical Models*