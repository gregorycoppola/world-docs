# The Negation Factor (Ψ_neg)

## Purpose

The negation factor enforces the constraint:

    P(p) + P(¬p) = 1

It links a proposition to its negation, enabling contrapositive reasoning.

## Definition

The negation factor is a hard constraint:

    Ψ_neg(p, ¬p) = {
        1  if p + ¬p = 1  (exactly one is true)
        0  otherwise
    }

Equivalently:

    Ψ_neg(p=0, ¬p=1) = 1
    Ψ_neg(p=1, ¬p=0) = 1
    Ψ_neg(p=0, ¬p=0) = 0  (contradiction)
    Ψ_neg(p=1, ¬p=1) = 0  (contradiction)

## When Negation Factors Are Created

A negation factor is created when:
1. Both P and "not P" appear in the knowledge base
2. We need to propagate constraints between them

Example:

    man(zeus) -> mortal(zeus)     # rule
    not mortal(zeus)               # fact
    
    Creates: Ψ_neg linking mortal(zeus) ↔ not mortal(zeus)

## Forward Message (π)

If one side is evidence, propagate to the other:

    # If ¬p is evidence=True
    π(p=0) = π(¬p=1) = 1.0
    π(p=1) = π(¬p=0) = 0.0
    
    # If p is evidence=True
    π(¬p=0) = π(p=1) = 1.0
    π(¬p=1) = π(p=0) = 0.0

In general:

    π(p) = [π(¬p)[1], π(¬p)[0]]  # swap
    π(¬p) = [π(p)[1], π(p)[0]]    # swap

## Backward Message (λ)

Constraints propagate through negation by swapping:

    λ(p) = [λ(¬p)[1], λ(¬p)[0]]
    λ(¬p) = [λ(p)[1], λ(p)[0]]

**Example** (contrapositive):

    λ(¬p) = [0.0, 1.0]  # ¬p must be true
    
    λ(p) = [1.0, 0.0]   # therefore p must be false

## Role in Contrapositive Reasoning

The negation factor is essential for contrapositive inference.

**Scenario**:

    man(x) -> mortal(x)
    not mortal(zeus)  # evidence
    ? man(zeus)

**Without Ψ_neg**:
- mortal(zeus) has no direct evidence
- man(zeus) stays at prior (0.5)
- Contrapositive fails

**With Ψ_neg**:
1. not mortal(zeus) = True (evidence)
2. Ψ_neg propagates: λ(mortal(zeus)=1) = 0
3. OR backward: λ(group=1) = 0
4. AND backward: λ(man(zeus)=1) = 0
5. Result: P(man(zeus)) = 0

## Implementation Detail

In the factor graph, Ψ_neg connects two proposition nodes:

    p_positive ←——[Ψ_neg]——→ p_negative

Both nodes exist as separate variables, but the factor ensures they're always opposite.

## Why a Separate Factor?

We could handle negation differently:
1. Store only positive propositions, compute ¬p = 1 - p
2. Use a single variable with negated queries

We use a separate factor because:
1. **Explicit representation**: Both P and ¬P can be evidence
2. **Clean message passing**: Standard BP on the factor graph
3. **Symmetry**: Negation is just another factor type

## Consistency

The negation factor ensures logical consistency:

    P(mortal(zeus)) + P(not mortal(zeus)) = 1

This is guaranteed by the factor structure, not by post-hoc normalization.