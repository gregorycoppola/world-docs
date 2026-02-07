# Inference Overview

## The Problem

Given:
- A knowledge base of facts and rules
- A query proposition

Compute:
- P(query = true | evidence)

This is probabilistic inference over a logical knowledge base.

## What Makes It Hard

### Exponential State Space

For N boolean propositions, there are 2^N possible worlds. Naive enumeration is intractable.

### Cycles

Rules can create cycles in the dependency graph:

    trust(a, b) & trust(b, a) -> allies(a, b)
    allies(a, b) -> cooperate(a, b)
    cooperate(a, b) -> trust(a, b)

Cycles require iterative methods.

### Combining Evidence

Multiple rules may support the same conclusion:

    smart(x) -> successful(x)
    hardworking(x) -> successful(x)
    lucky(x) -> successful(x)

How do these combine?

## Our Approach

### Factor Graph Representation

Convert the logical KB to a factor graph:
- **Variable nodes**: Propositions and groups
- **Factor nodes**: Ψ_and, Ψ_or, Ψ_neg

### Belief Propagation

Use message passing to compute marginal probabilities:
- **π messages**: Forward (causes → effects)
- **λ messages**: Backward (effects → causes)

### Noisy-OR Combination

Multiple causes combine via noisy-OR:

    P(effect | causes) = 1 - ∏ᵢ (1 - wᵢ × causeᵢ)

Each cause independently "tries" to produce the effect.

## The Inference Pipeline

    1. Ground rules for relevant entities
    2. Build factor graph from query
    3. Set evidence (known facts)
    4. Run belief propagation
    5. Read off P(query)

## Key Properties

### Soundness

If the graph is a tree (no cycles), BP gives exact probabilities.

### Completeness

BP visits all relevant propositions via forward/backward expansion.

### Efficiency

O(N × 2^k × iterations) where:
- N = number of variables
- k = max premises per rule
- iterations = typically < 20

### Interpretability

Every probability has a derivation:
- Which rules fired
- Which evidence contributed
- The path through the graph