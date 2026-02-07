# Related Work Overview

## The Landscape

QBBN sits at the intersection of several research traditions:

1. **Statistical Relational Learning** — Combining logic and probability
2. **Probabilistic Logic Programming** — Logic programs with uncertainty
3. **Neural Theorem Proving** — Using neural networks to guide proof search
4. **Neuro-Symbolic AI** — Combining neural perception with symbolic reasoning

Each approach makes different tradeoffs between expressiveness, tractability, and learnability.

## Positioning

| System | Logic | Probability | Inference | Learning |
|--------|-------|-------------|-----------|----------|
| FOL Theorem Provers | Full FOL | None | Complete | None |
| Bayesian Networks | Propositional | Full | BP/Exact | Parameters |
| Markov Logic Networks | Full FOL | Full | MCMC | Structure+Params |
| ProbLog | Logic Programs | Independent | Model Counting | Parameters |
| Neural Theorem Provers | Varies | Implicit | Learned | End-to-end |
| QBBN | Horn Clauses | Noisy-OR | BP | Parameters |

## QBBN's Niche

QBBN trades expressiveness for efficiency:

**What we give up**:
- Full first-order logic (only Horn clauses)
- Arbitrary probability distributions (only noisy-OR)
- Complex quantifier interactions

**What we gain**:
- Efficient inference (BP instead of MCMC)
- Interpretable structure (explicit factor graph)
- Tractable learning (convex optimization for weights)
- Natural language alignment (role-based predicates)

## Key Comparisons

The following documents compare QBBN to specific related systems:

- `02_markov_logic_networks.md` — The dominant probabilistic logic approach
- `03_problog.md` — Probabilistic logic programming
- `04_neural_theorem_provers.md` — Neural approaches to reasoning
- `05_neuro_symbolic.md` — The broader integration effort