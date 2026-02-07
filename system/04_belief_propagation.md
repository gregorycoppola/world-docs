# Belief Propagation Implementation

## Overview

We implement Pearl's belief propagation algorithm with separate π (forward) and λ (backward) messages. This enables both:
- Forward inference: causes → effects
- Backward inference: effects → causes (contrapositive)

## Data Structures

### Per-Variable State

For each variable (proposition or group):

    pi[var_id] = [P(var=0), P(var=1)]   # forward belief from causes
    lam[var_id] = [λ(var=0), λ(var=1)]  # backward belief from effects

### Evidence Initialization

For evidence=True:

    pi[var_id] = [0.0, 1.0]   # definitely true
    lam[var_id] = [0.0, 1.0]  # constraint: must be true

For evidence=False:

    pi[var_id] = [1.0, 0.0]   # definitely false
    lam[var_id] = [1.0, 0.0]  # constraint: must be false

For unknown:

    pi[var_id] = [0.5, 0.5]   # prior: unknown
    lam[var_id] = [1.0, 1.0]  # no constraint

## Message Passing

### Forward Pass (π Messages)

Information flows from causes to effects.

**AND factors** (propositions → groups):

    # g = p1 ∧ p2 ∧ ... ∧ pn
    prob_all_true = ∏ᵢ pi[pᵢ][1]
    pi[g] = [1 - prob_all_true, prob_all_true]

**OR factors** (groups → propositions):

    # Noisy-OR: p is caused by any group
    prob_not_caused = ∏ᵢ ((1 - g_prob) + g_prob × leak)
    pi[p] = [prob_not_caused, 1 - prob_not_caused]

Where leak = exp(-weight) for soft rules, leak = 0 for deterministic rules.

**NEG factors** (bidirectional):

    # pos and neg are linked: pos = 1 - neg
    pi[pos] = [pi[neg][1], pi[neg][0]]

### Backward Pass (λ Messages)

Information flows from effects to causes.

**OR factors** (propositions → groups):

For deterministic rules: if conclusion must be false, groups must be false.

    lam_g_0 = lam_p[0] + lam_p[1]  # g=0 always consistent
    lam_g_1 = lam_p[1]              # g=1 only if p=1

**AND factors** (groups → propositions):

If group must be false, at least one premise must be false.

    lam_p_1 = other_prob_true × lam_g[1] + (1 - other_prob_true) × lam_g[0]
    lam_p_0 = lam_g[0]  # p=0 means g=0

**NEG factors** (bidirectional):

    # λ flows through negation
    lam[pos] = [lam[neg][1], lam[neg][0]]

### Belief Computation

Combine π and λ to get posterior:

    p0 = pi[var][0] × lam[var][0]
    p1 = pi[var][1] × lam[var][1]
    belief = p1 / (p0 + p1)

## Iteration

The algorithm iterates:

1. Initialize π and λ for all variables
2. Forward pass: update π through AND, OR, NEG factors
3. Backward pass: update λ through OR, AND, NEG factors
4. Compute beliefs with damping
5. Check convergence
6. Repeat until converged or max iterations

## Damping

Damping prevents oscillation in loopy graphs:

    new_belief = damping × old_belief + (1 - damping) × computed_belief

With damping=0.5, each update is averaged with the previous value.

## Example: Contrapositive Trace

**Setup**:

    man(zeus) -> mortal(zeus)
    evidence: mortal(zeus) = False
    query: man(zeus)

**Initialization**:

    p1 (man):    π=[0.5, 0.5], λ=[1.0, 1.0]
    p2 (mortal): π=[1.0, 0.0], λ=[1.0, 0.0]  # evidence=False
    g1 (group):  π=[0.5, 0.5], λ=[1.0, 1.0]

**Iteration 1**:

    Forward:
      AND: π(g1) = [0.5, 0.5]
      
    Backward:
      OR:  λ(g1) = [1.0, 0.0]  # from λ(p2)=[1.0, 0.0]
      AND: λ(p1) = [1.0, 0.0]  # from λ(g1)=[1.0, 0.0]
    
    Beliefs:
      p1: π×λ = [0.5, 0.5] × [1.0, 0.0] = [0.5, 0.0] → P=0.0

**Result**: P(man(zeus)) = 0.0 ✓