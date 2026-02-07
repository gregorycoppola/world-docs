# ProbLog and Probabilistic Logic Programming

## Overview

ProbLog is a probabilistic extension of Prolog, developed at KU Leuven. It represents a different approach to combining logic and probability than MLNs.

## How ProbLog Works

**Probabilistic facts**: Each fact has an independent probability of being true.

    0.3::stress(X) :- person(X).
    0.2::influences(X,Y) :- friend(X,Y).

**Deterministic rules**: Standard Prolog rules derive conclusions.

    smokes(X) :- stress(X).
    smokes(X) :- friend(X,Y), influences(X,Y), smokes(Y).

**Semantics**: The probability of a query is computed by summing over all possible "worlds" (combinations of probabilistic facts).

    P(query) = Σ_{world} P(world) × I(query true in world)

## Inference

**Exact inference**: Weighted model counting.
- Convert program to Boolean formula
- Use knowledge compilation (BDDs, SDDs)
- Exact but can be expensive

**Approximate inference**: Sampling-based.
- Sample probabilistic facts
- Run deterministic inference
- Average results

## Comparison to QBBN

| Aspect | ProbLog | QBBN |
|--------|---------|------|
| Probability model | Independent facts | Noisy-OR rules |
| Inference | Model counting | Belief propagation |
| Cycles | Handled via fixpoint | Loopy BP |
| Learning | Parameter estimation | Weight learning |

### Key Differences

**Independence assumption**: ProbLog assumes probabilistic facts are independent. QBBN models dependencies via the factor graph.

**Rule interpretation**: 
- ProbLog: Rules are deterministic given the facts
- QBBN: Rules themselves have probabilistic strength (modals)

**Inference mechanism**:
- ProbLog: Compile to formula, count models
- QBBN: Message passing on factor graph

### Example Comparison

**ProbLog**:

    0.3::rainy.
    0.5::sprinkler.
    wet :- rainy.
    wet :- sprinkler.
    
    P(wet) = 1 - (1-0.3)(1-0.5) = 0.65

**QBBN**:

    sometimes: rainy() -> wet()
    sometimes: sprinkler() -> wet()
    rainy()   # evidence: true
    
    P(wet) computed via noisy-OR

Both give similar results but via different mechanisms.

## ProbLog Extensions

**DeepProbLog**: Integrates neural networks with ProbLog.
- Neural predicates whose truth is determined by a neural network
- Enables learning from raw data

**ProbLog2**: Improved implementation with:
- Better knowledge compilation
- Approximate inference
- Parameter learning

## When to Use What

**Use ProbLog when**:
- Independent probabilistic facts fit your domain
- You want exact inference
- Logic programming style is natural

**Use QBBN when**:
- Causes/effects are the natural model
- You need very fast inference
- Cyclic dependencies exist

## Key References

- De Raedt, L., Kimmig, A., & Toivonen, H. (2007). "ProbLog: A Probabilistic Prolog"
- Fierens, D., et al. (2015). "Inference and Learning in Probabilistic Logic Programs using Weighted Boolean Formulas"
- Manhaeve, R., et al. (2018). "DeepProbLog: Neural Probabilistic Logic Programming"