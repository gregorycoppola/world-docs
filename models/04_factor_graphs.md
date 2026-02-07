# Factor Graphs

## What is a Factor Graph?

A factor graph is an undirected bipartite graph that represents the factorization of a function. It consists of:

1. **Variable nodes**: Represent random variables
2. **Factor nodes**: Represent functions over subsets of variables
3. **Edges**: Connect each factor to the variables in its scope

Factor graphs generalize both Bayesian networks and Markov random fields.

## Formal Definition

A factor graph represents a function that factorizes as:

    f(x₁, ..., xₙ) = ∏ₐ fₐ(xₐ)

Where:
- xₐ is the subset of variables connected to factor a
- fₐ is the factor function

For probability distributions:

    P(x₁, ..., xₙ) = (1/Z) ∏ₐ fₐ(xₐ)

Where Z is the normalizing constant (partition function).

## Example

Consider P(A, B, C) = f₁(A, B) × f₂(B, C) × f₃(C)

Factor graph:

    (A)---[f₁]---(B)---[f₂]---(C)---[f₃]

Variable nodes: circles (A), (B), (C)
Factor nodes: squares [f₁], [f₂], [f₃]

## Factor Graph vs. Bayesian Network

A Bayesian network A → B → C can be converted to a factor graph:

    (A)---[P(A)]
    
    (A)--[P(B|A)]--(B)
    
    (B)--[P(C|B)]--(C)

The conditional probability tables become factors.

## Belief Propagation on Factor Graphs

The sum-product algorithm on factor graphs:

**Variable to factor message**:

    μₓ→ₐ(x) = ∏_{b ∈ N(x) \ a} μb→x(x)

**Factor to variable message**:

    μₐ→ₓ(x) = Σ_{xₐ\x} fₐ(xₐ) ∏_{y ∈ N(a) \ x} μᵧ→ₐ(y)

**Marginal belief**:

    P(x) ∝ ∏_{a ∈ N(x)} μₐ→ₓ(x)

## The Partition Function Problem

Computing Z = Σₓ ∏ₐ fₐ(xₐ) is #P-hard in general.

This is why exact inference is intractable for most graphical models.

Approximations:
- Loopy BP (ignores the partition function)
- Variational methods (bound the partition function)
- Sampling (estimate the partition function)

## Special Factor Types

**Pairwise factors**: Connect exactly two variables
- Common in vision (neighboring pixels)
- Enable efficient algorithms

**Higher-order factors**: Connect many variables
- More expressive
- More expensive

**Deterministic factors**: Output is determined by inputs
- AND, OR, XOR gates
- Enable logical constraints

## Relevance to QBBN

QBBN is a factor graph with specific structure:

**Variable nodes**:
- Proposition nodes (p): Single grounded predicates
- Group nodes (g): Conjunctions of propositions

**Factor nodes**:

- Ψ_and: Deterministic AND factor

      Ψ_and(g | p₁, ..., pₙ) = 1 if g = (p₁ ∧ ... ∧ pₙ), else 0
  
- Ψ_or: Noisy-OR factor (learned weights)

      Ψ_or(p | g₁, ..., gₘ) = 1 - ∏ᵢ (1 - wᵢ × gᵢ)
  
- Ψ_neg: Deterministic negation factor

      Ψ_neg(p, ¬p) = 1 if p + ¬p = 1, else 0

**Structure**:

    p → [Ψ_and] → g → [Ψ_or] → p

The bipartite structure (propositions ↔ groups) with alternating AND/OR factors is what enables efficient inference.

## Key References

- Kschischang, F., Frey, B., & Loeliger, H. (2001). "Factor Graphs and the Sum-Product Algorithm"
- Koller, D. & Friedman, N. (2009). *Probabilistic Graphical Models*
- Bishop, C. M. (2006). *Pattern Recognition and Machine Learning*, Chapter 8