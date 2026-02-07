# Neural Theorem Provers

## Overview

Neural theorem provers use neural networks to guide logical reasoning. Instead of hand-coded heuristics, they learn to prove theorems from data.

## Approaches

### Neural Proof Search

Use neural networks to select which inference rule to apply next.

**Architecture**:
- Encode current proof state
- Score possible next steps
- Select highest-scoring action
- Repeat until proof found or timeout

**Examples**:
- DeepMath (Alemi et al., 2016)
- GamePad (Huang et al., 2019)
- GPT-f (Polu & Sutskever, 2020)

### Neural Unification

Learn embeddings for logical terms, use similarity for unification.

**Idea**: Instead of symbolic unification, embed terms in vector space and use soft matching.

**Examples**:
- Neural Theorem Prover (Rocktäschel & Riedel, 2017)

### Neural Logic Programming

Differentiable versions of logic programming.

**Examples**:
- Neural Logic Machines (Dong et al., 2019)
- Differentiable Inductive Logic Programming (Evans & Grefenstette, 2018)

## AlphaGeometry

Recent breakthrough: AlphaGeometry (Trinh et al., 2024) solves olympiad geometry problems.

**Approach**:
- Neural language model proposes constructions
- Symbolic solver checks validity
- Alternates until solution found

**Results**: Solves 25/30 olympiad problems (near gold medalist level).

**Relevance**: Shows neural + symbolic combination can exceed either alone.

## Comparison to QBBN

| Aspect | Neural Theorem Provers | QBBN |
|--------|----------------------|------|
| Structure | Learned | Explicit |
| Inference | Guided search | BP |
| Interpretability | Low | High |
| Training data | Proofs needed | Facts suffice |
| Expressiveness | Full logic | Horn clauses |

### Different Goals

**Neural theorem provers aim to**:
- Prove difficult theorems
- Match human mathematicians
- Handle complex, creative reasoning

**QBBN aims to**:
- Answer factual queries
- Fast, reliable inference
- Interpretable reasoning chains

### Complementary Approaches

Neural methods excel at:
- Learning heuristics from data
- Handling novel problem structures
- Flexible, creative search

Symbolic methods (including QBBN) excel at:
- Guaranteed sound inference
- Interpretable explanations
- Efficient routine reasoning

**Hybrid**: Use neural networks to parse and propose, QBBN to verify and reason.

## Key References

- Rocktäschel, T. & Riedel, S. (2017). "End-to-end Differentiable Proving"
- Evans, R. & Grefenstette, E. (2018). "Learning Explanatory Rules from Noisy Data"
- Polu, S. & Sutskever, I. (2020). "Generative Language Modeling for Automated Theorem Proving"
- Trinh, T., et al. (2024). "Solving Olympiad Geometry without Human Demonstrations"