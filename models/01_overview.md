# Models Overview

## Two Traditions

The QBBN sits at the intersection of two major traditions in AI and machine learning:

1. **Probabilistic Graphical Models** — Bayesian networks, belief propagation, factor graphs
2. **Neural Networks** — Deep learning, attention, transformers

These traditions have developed largely in parallel, with different strengths:

| Aspect | Graphical Models | Neural Networks |
|--------|------------------|-----------------|
| Interpretability | High (explicit structure) | Low (black box) |
| Reasoning | Principled (probability theory) | Emergent (pattern matching) |
| Learning | Structure + parameters | End-to-end |
| Data efficiency | High (with good priors) | Low (needs lots of data) |
| Flexibility | Limited (fixed structure) | High (learns representations) |

## The QBBN Approach

QBBN takes the graphical model approach but learns from the neural network tradition:

- **From graphical models**: Factor graph structure, belief propagation, probabilistic semantics
- **From neural networks**: The idea that features can be learned, composition of simple units

The key insight is that logical reasoning can be cast as belief propagation on a factor graph where:
- Nodes are propositions (boolean random variables)
- Factors are AND gates (conjunction) and OR gates (disjunction)
- Weights on OR gates can be learned from data

## Document Structure

This section covers the background needed to understand QBBN:

**Probabilistic Graphical Models**
- `02_bayesian_networks.md` — Directed graphical models, conditional probability
- `03_belief_propagation.md` — Message passing algorithms for inference
- `04_factor_graphs.md` — Undirected models, factors, marginalization

**Logic and Inference**
- `05_propositional_logic.md` — Boolean algebra, satisfiability
- `06_first_order_logic.md` — Predicates, quantifiers, unification
- `07_logic_and_probability.md` — Markov logic, probabilistic logic programming
- `12_computability.md` — FOL and Turing equivalence, Church-Turing thesis

**Neural Networks**
- `08_neural_networks.md` — Perceptrons, backpropagation, deep learning
- `09_attention.md` — Attention mechanisms, key-query-value
- `10_transformers.md` — The transformer architecture, LLMs

**Proof Theory**
- `11_prawitz_natural_deduction.md` — Natural deduction, fragment hierarchy, modal logic

## The Convergence

Recent work has seen these traditions converge:

- **Neural theorem provers** — Using neural networks to guide logical search
- **Graph neural networks** — Message passing on learned representations
- **Neuro-symbolic AI** — Combining neural perception with symbolic reasoning

QBBN represents one point in this design space: a classical graphical model structure with the potential for learned weights, designed specifically for natural language reasoning.