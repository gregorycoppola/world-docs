# Message Passing Equations

## Overview

Belief propagation computes marginal probabilities via message passing. Each variable maintains:

- **π**: Forward belief (from causes)
- **λ**: Backward belief (from effects)
- **Belief**: Combined posterior

## Notation

Variables:
- p, q: propositions
- g: groups
- z: generic variable (either p or g)

Messages:
- π(z) = [P(z=0), P(z=1)]: forward belief
- λ(z) = [λ(z=0), λ(z=1)]: backward constraint
- πz(parent): message from parent to z
- λz(child): message from child to z

## Initialization

### Evidence Variables

For variable z with evidence=True:

    π(z) = [0.0, 1.0]
    λ(z) = [0.0, 1.0]

For variable z with evidence=False:

    π(z) = [1.0, 0.0]
    λ(z) = [1.0, 0.0]

### Non-Evidence Variables

    π(z) = [0.5, 0.5]  # uniform prior
    λ(z) = [1.0, 1.0]  # no constraint

## AND Factor Messages

Factor: g = p₁ ∧ p₂ ∧ ... ∧ pₙ

### Forward (π): Premises → Group

    π(g=1) = ∏ᵢ π(pᵢ=1)
    π(g=0) = 1 - π(g=1)

### Backward (λ): Group → Premises

For each premise pᵢ:

    other_prob = ∏_{j≠i} π(pⱼ=1)
    
    λ(pᵢ=1) = other_prob × λ(g=1) + (1 - other_prob) × λ(g=0)
    λ(pᵢ=0) = λ(g=0)

**Derivation**:

    λ(pᵢ=v) = Σ_g P(g | pᵢ=v, others) × λ(g)

When pᵢ=0: g=0 always, so λ(pᵢ=0) = λ(g=0)
When pᵢ=1: g=1 if all others=1 (prob = other_prob), else g=0

## OR Factor Messages

Factor: p ← g₁ ∨ g₂ ∨ ... ∨ gₘ (noisy-OR)

### Forward (π): Groups → Conclusion

    base_leak = 0.001
    prob_not_caused = 1 - base_leak
    
    for each gᵢ:
        if weightᵢ ≥ 50:
            leakᵢ = 0
        else:
            leakᵢ = exp(-weightᵢ)
        
        prob_not_caused *= (1 - π(gᵢ=1)) + π(gᵢ=1) × leakᵢ
    
    π(p=1) = 1 - prob_not_caused
    π(p=0) = prob_not_caused

### Backward (λ): Conclusion → Groups

For each group gᵢ:

    if weightᵢ ≥ 50:  # deterministic
        λ(gᵢ=0) = λ(p=0) + λ(p=1)
        λ(gᵢ=1) = λ(p=1)
    else:  # soft
        leakᵢ = exp(-weightᵢ)
        λ(gᵢ=0) = λ(p=0) + λ(p=1)
        λ(gᵢ=1) = λ(p=0) × leakᵢ + λ(p=1)

**Intuition**: 
- gᵢ=0 is always consistent with any p
- gᵢ=1 with deterministic weight forces p=1
- gᵢ=1 with soft weight allows p=0 with probability leak

## NEG Factor Messages

Factor: p ↔ ¬p (negation constraint)

### Forward (π)

    π(p) = [π(¬p)[1], π(¬p)[0]]
    π(¬p) = [π(p)[1], π(p)[0]]

### Backward (λ)

    λ(p) = [λ(¬p)[1], λ(¬p)[0]]
    λ(¬p) = [λ(p)[1], λ(p)[0]]

Simply swap the values for 0 and 1.

## Belief Computation

The posterior probability is:

    unnorm_0 = π(z=0) × λ(z=0)
    unnorm_1 = π(z=1) × λ(z=1)
    
    P(z=1 | evidence) = unnorm_1 / (unnorm_0 + unnorm_1)

**Derivation**:

    P(z | evidence) ∝ P(z | causes) × P(effects | z)
                    ∝ π(z) × λ(z)

## Iteration Schedule

Each iteration:

    1. Forward pass:
       - Update π through all AND factors
       - Update π through all OR factors
       - Update π through all NEG factors
    
    2. Backward pass:
       - Update λ through all OR factors
       - Update λ through all AND factors
       - Update λ through all NEG factors
    
    3. Compute beliefs for all variables

## Example Trace

Setup:

    man(zeus) -> mortal(zeus)  [weight=99]
    evidence: mortal(zeus) = False
    query: man(zeus)

Variables:
- p1: man(zeus)
- p2: mortal(zeus) [evidence=False]
- g1: group(p1)

Initialization:

    p1: π=[0.5, 0.5], λ=[1.0, 1.0]
    p2: π=[1.0, 0.0], λ=[1.0, 0.0]
    g1: π=[0.5, 0.5], λ=[1.0, 1.0]

Iteration 1 - Forward:

    AND: π(g1) = [1 - 0.5, 0.5] = [0.5, 0.5]
    OR: p2 is evidence, skip

Iteration 1 - Backward:

    OR: λ(p2) = [1.0, 0.0]
        weight = 99, so leak = 0
        λ(g1=0) = 1.0 + 0.0 = 1.0
        λ(g1=1) = 0.0
        λ(g1) = [1.0, 0.0]
    
    AND: λ(g1) = [1.0, 0.0]
         other_prob = 1.0 (no other premises)
         λ(p1=1) = 1.0 × 0.0 + 0.0 × 1.0 = 0.0
         λ(p1=0) = 1.0
         λ(p1) = [1.0, 0.0]

Belief:

    p1: unnorm = [0.5 × 1.0, 0.5 × 0.0] = [0.5, 0.0]
        P(p1=1) = 0.0 / 0.5 = 0.0

Result: P(man(zeus)) = 0.0 ✓