# Markov Logic Networks

## Overview

Markov Logic Networks (MLNs) are the most influential approach to combining first-order logic with probability. Introduced by Richardson & Domingos (2006), MLNs attach weights to first-order formulas.

## How MLNs Work

**Definition**: An MLN is a set of pairs (Fᵢ, wᵢ) where Fᵢ is a first-order formula and wᵢ is a real-valued weight.

**Semantics**: Given a set of constants, the MLN defines a probability distribution over possible worlds:

    P(x) = (1/Z) exp(Σᵢ wᵢ nᵢ(x))

Where:
- x is a possible world (assignment of truth values to all ground atoms)
- nᵢ(x) is the number of true groundings of formula Fᵢ in world x
- Z is the partition function (normalization constant)

**Example**:

    1.5: friends(x, y) ∧ smokes(x) → smokes(y)
    2.0: ¬smokes(x)

Higher weight = stronger preference for worlds satisfying the formula.

## Inference in MLNs

**Challenge**: Computing P(query|evidence) requires summing over exponentially many possible worlds.

**Approaches**:
- MCMC sampling (MC-SAT, Gibbs sampling)
- Lifted inference (exploit symmetry)
- Variational methods
- Cutting plane inference

All are expensive for large domains.

## Learning in MLNs

**Weight learning**: Given structure, learn weights from data.
- Pseudo-likelihood maximization
- Gradient descent

**Structure learning**: Learn formulas from data.
- Inductive Logic Programming style
- Very expensive

## QBBN vs. MLNs

| Aspect | MLN | QBBN |
|--------|-----|------|
| Logic | Full FOL | Horn clauses |
| Probability | Log-linear | Noisy-OR |
| Inference | MCMC (slow) | BP (fast) |
| Partition function | Must compute Z | Avoided by BP |
| Expressiveness | Higher | Lower |
| Tractability | Lower | Higher |

### Inference Comparison

MLN inference is #P-hard. Even approximate inference (MCMC) can require many samples to converge.

QBBN inference via BP:
- O(N × 2^k) per iteration where k is max rule arity
- Empirically converges in few iterations
- No partition function computation needed

### Expressiveness Comparison

MLNs can express:
- Arbitrary first-order formulas
- Complex quantifier interactions
- Arbitrary correlations between atoms

QBBN is limited to:
- Horn clauses (implication with conjunction of premises)
- Noisy-OR combination of causes
- No negative premises in rules

### When to Use What

**Use MLNs when**:
- You need full FOL expressiveness
- Domain is small enough for MCMC
- Complex correlations matter

**Use QBBN when**:
- Knowledge is mostly causal/implicational
- Fast inference is required
- Interpretability is important

## Key References

- Richardson, M. & Domingos, P. (2006). "Markov Logic Networks"
- Domingos, P. & Lowd, D. (2009). "Markov Logic: An Interface Layer for AI"
- Khot, T., et al. (2011). "Learning Markov Logic Networks via Functional Gradient Boosting"