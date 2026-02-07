# Future Work: Learning

## Overview

Currently, QBBN uses hand-specified rules with manually assigned weights. A complete system would learn both:

1. **Structure**: What rules exist
2. **Parameters**: What weights rules have

## Weight Learning

### The Problem

Given:
- A set of rules with unknown weights
- Training data (observed facts)

Learn:
- Weights that maximize likelihood of observations

### Approach: Gradient Descent

The noisy-OR model is differentiable. For a rule with weight w:

    P(conclusion | premises) = 1 - exp(-w)  (when premises true)

Log-likelihood:

    L = Σ log P(observed_conclusion | premises)

Gradient:

    ∂L/∂w = Σ (observed - predicted) × premise_indicator

This is convex when premises are fully observed.

### Challenges

**Latent variables**: When some propositions are unobserved, we need EM or variational methods.

**Cycles**: Cyclic dependencies complicate the likelihood.

**Scale**: Many rules, many entities, many training examples.

### Existing Work

The original QBBN paper demonstrated weight learning on synthetic data:
- Generated data from known weights
- Recovered weights via SGD
- Showed convergence on dating/attraction example

Next steps:
- Scale to larger KBs
- Handle partial observations
- Integrate with structure learning

## Structure Learning

### The Problem

Given:
- A set of predicates
- Training data (observed facts)

Learn:
- What rules should exist

This is much harder than weight learning.

### Approach 1: Inductive Logic Programming (ILP)

Search for rules that explain the data:

    Observation: mortal(socrates), mortal(plato), mortal(aristotle)
    Background: man(socrates), man(plato), man(aristotle)
    
    Induced rule: man(X) -> mortal(X)

**Methods**:
- FOIL (top-down rule construction)
- Progol (inverse entailment)
- ILASP (answer set programming)

**Challenge**: Combinatorial search space.

### Approach 2: Neural Rule Induction

Use neural networks to propose rules:

    Input: Knowledge graph embeddings
    Output: Candidate rules with scores
    
    Filter: Keep rules that improve likelihood

**Methods**:
- NeuralLP
- DRUM
- Neural Theorem Provers

**Advantage**: Can leverage large pre-trained models.

### Approach 3: LLM-Based Rule Extraction

Use language models to propose rules from text:

    Input: "Dogs are mammals. Mammals are animals."
    Output: dog(X) -> mammal(X)
            mammal(X) -> animal(X)

This is essentially what our parsing pipeline does, but could be extended to:
- Extract implicit rules from large corpora
- Propose common-sense rules
- Fill gaps in the KB

### Challenges

**Evaluation**: How do we know a learned rule is correct?

**Overfitting**: Rules that fit training data but don't generalize.

**Negative examples**: Learning what's NOT true is hard.

**Compositionality**: Learning rules that combine in complex ways.

## Joint Structure-Parameter Learning

The holy grail: learn both structure and weights together.

### EM-Style Approach

    E-step: Given current rules/weights, compute expected counts
    M-step: Update weights, propose/prune rules
    Repeat

### Bayesian Approach

Put priors on both structure and weights:

    P(rules, weights | data) ∝ P(data | rules, weights) × P(weights) × P(rules)

Use MCMC or variational inference.

### Neural Approach

End-to-end differentiable:

    Text → Neural Parser → Soft Rules → Differentiable Inference → Loss

Backpropagate through entire pipeline.

## Practical Path Forward

### Phase 1: Weight Learning (Near-term)

- Implement gradient descent for weights
- Use synthetic data for validation
- Apply to coverage tests

### Phase 2: Rule Refinement (Medium-term)

- Start with LLM-extracted rules
- Learn weights from data
- Prune low-weight rules
- Propose variations of successful rules

### Phase 3: Full Structure Learning (Long-term)

- Combine neural rule proposal with symbolic verification
- Use QBBN inference to score candidate rules
- Iteratively grow the KB