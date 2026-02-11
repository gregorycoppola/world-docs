# Contribution 1: Inference

## The Problem

Given a knowledge base of logical facts and rules and a query, compute the probability that the query is true given the evidence. Do this efficiently, interpretably, and with support for both forward reasoning (facts imply conclusions) and backward reasoning (negative evidence constrains premises).

## What Existed Before

**Markov Logic Networks** (Richardson and Domingos, 2006): Full first-order logic with weights, inference via MCMC sampling. Slow, doesn't scale, hard to interpret.

**ProbLog** (De Raedt et al., 2007): Probabilistic Prolog with inference via weighted model counting. Exact but computationally expensive for large knowledge bases.

**Standard Bayesian Networks**: Efficient belief propagation, but propositional only — no quantified rules, no logical structure.

None of these decomposed logical rules into boolean factors and ran belief propagation over the result.

## What We Contribute

### The Boolean Decomposition (2024 Paper)

All logical structure is reduced to three factor types on a bipartite graph:

**AND factors (Ψ_and)**: Deterministic conjunction. A group g = p₁ ∧ ... ∧ pₙ is true iff all premises are true. Implements ∧-Introduction from Prawitz's natural deduction.

**OR factors (Ψ_or)**: Weighted disjunction via noisy-OR. A proposition p supported by groups g₁...gₙ with weights w₁...wₙ: P(p) = 1 - ∏ᵢ(1 - wᵢ · P(gᵢ)). Implements ∨-Introduction and →-Elimination (modus ponens).

**NEG factors (Ψ_neg)** (this paper): Links a proposition p to its negation ¬p, enforcing P(p) + P(¬p) = 1. Enables contrapositive reasoning — when negative evidence is set on a conclusion, it propagates backward through the rule to constrain premises. This is modus tollens.

### The NEG Factor Innovation

The 2024 paper had AND and OR only. Forward reasoning worked — modus ponens, chaining, noisy-OR combining. But contrapositive reasoning did not. Given `man(x) -> mortal(x)` and `not mortal(zeus)`, the system could not infer `not man(zeus)`.

The NEG factor solves this. It creates a link between every proposition and its negation. When `not mortal(zeus)` is set as evidence (true), the NEG factor forces `mortal(zeus)` to be false. The backward λ messages then propagate through the OR factor to the group, through the AND factor to the premise `man(zeus)`, constraining it to be false.

This completes the implementation of Prawitz's forward fragment. With AND + OR, you get modus ponens. With AND + OR + NEG, you get both modus ponens and modus tollens — the full set of simple elimination rules operating in both directions via a single belief propagation loop.

### Bidirectional Proposition Graph Construction

The proposition graph is built query-driven — only the subgraph relevant to answering the query is constructed. The 2024 paper built graphs backward only (from query to premises). This paper adds forward expansion: when a proposition is added to the graph, the engine also checks whether it appears as a premise in any rule and adds those conclusions too.

This bidirectional construction is necessary for contrapositive reasoning. The NEG factor needs both the positive and negative versions of propositions in the graph. The forward expansion ensures that evidence propositions are connected to their consequences, so that backward λ messages have paths to travel.

### Belief Propagation

Pearl-style belief propagation with separate π (forward/causal) and λ (backward/evidential) messages, damping factor 0.5. Each iteration:

1. Compute AND factor π messages (forward through premises to groups)
2. Compute OR factor π messages (forward through groups to conclusions)
3. Compute OR factor λ messages (backward from conclusions to groups)
4. Update beliefs: P(z) = normalize(π(z) · λ(z))
5. Apply damping and check convergence

All 44 tests converge within 20 iterations. Most converge in 2–3.

### Connection to Prawitz

The three factor types correspond to the simple elimination rules of natural deduction (Prawitz, 1965):

| Prawitz Rule | QBBN Factor | Direction |
|---|---|---|
| ∧-Introduction | Ψ_and: group true iff all premises true | Forward π |
| ∨-Introduction | Ψ_or: multiple groups support same conclusion | Forward π |
| →-Elimination (modus ponens) | Ψ_and → Ψ_or chain | Forward π |
| Contrapositive (modus tollens) | Ψ_neg + backward λ messages | Backward λ |

The system stays in the forward fragment — no case analysis (∨-Elimination), no hypothetical reasoning (→-Introduction), no witness search (∃-Elimination). This is why belief propagation converges quickly: the forward fragment is linear time.

## Evidence

44 out of 44 test cases pass across 22 reasoning categories including forward chaining, contrapositive reasoning, conjunction, disjunction, negation, identity, transitivity, symmetry, modality, and more. The contrapositive test was the hardest and motivated the NEG factor. The noisy-OR combining test demonstrates probabilistic reasoning over multiple independent rules.