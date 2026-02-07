# Case Study: Contrapositive Reasoning

## The Problem

Contrapositive reasoning was failing. Given:

    man(x) -> mortal(x)
    not mortal(zeus)
    ? man(zeus)

The system returned P = 0.5 (unknown) instead of P = 0.0 (no).

## Expected Reasoning

The contrapositive of "if A then B" is "if not B then not A".

    man(zeus) -> mortal(zeus)        # given rule
    not mortal(zeus)                  # given fact
    ∴ not man(zeus)                   # contrapositive

This is valid deductive inference. If Zeus is not mortal, and all men are mortal, Zeus cannot be a man.

## Root Cause

The belief propagation implementation was conflating π (forward) and λ (backward) messages into a single belief value.

**Original (broken) approach**:

    belief[var] = single probability value
    update based on parents' beliefs
    no backward flow from children

**Problem**: Evidence about the conclusion (mortal=False) never propagated backward to constrain the premise (man).

## The Fix

Implement proper Pearl-style belief propagation with separate π and λ:

**π (forward)**: Beliefs flowing from causes to effects.

    π[var] = [P(var=0), P(var=1)]

**λ (backward)**: Constraints flowing from effects to causes.

    λ[var] = [λ(var=0), λ(var=1)]

**Key insight for evidence**:

    if evidence = False:
        π[var] = [1.0, 0.0]  # definitely false
        λ[var] = [1.0, 0.0]  # MUST be false (constraint)

The λ value for evidence encodes the constraint, not just the belief.

## Message Flow

**Before fix** (broken):

    p1(man) = 0.5      # no information
    p2(mortal) = 0.0   # evidence
    
    Forward only: p1 stays at 0.5

**After fix** (working):

    Initialization:
      p1(man):    π=[0.5, 0.5], λ=[1.0, 1.0]
      p2(mortal): π=[1.0, 0.0], λ=[1.0, 0.0]  # evidence=False
      g1(group):  π=[0.5, 0.5], λ=[1.0, 1.0]
    
    Iteration 1:
      Forward:
        AND: π(g1) = [0.5, 0.5]
      
      Backward:
        OR:  λ(g1) = [1.0, 0.0]  # from λ(p2), weight=99
        AND: λ(p1) = [1.0, 0.0]  # from λ(g1)
    
      Belief:
        p1: π×λ = [0.5, 0.5] × [1.0, 0.0] → P = 0.0

## The Key Equations

**OR backward** (deterministic case):

    λ(g=0) = λ(p=0) + λ(p=1)  # g=0 consistent with any p
    λ(g=1) = λ(p=1)            # g=1 forces p=1

When λ(p=1) = 0 (conclusion cannot be true):

    λ(g=1) = 0  # group cannot be true either

**AND backward**:

    λ(premise=0) = λ(g=0)      # premise=0 means g=0
    λ(premise=1) = λ(g=1) × other_prob + λ(g=0) × (1 - other_prob)

When λ(g=1) = 0 (group cannot be true):

    λ(premise=1) = 0  # premise cannot be true

## Lessons Learned

1. **Separate π and λ**: Forward and backward beliefs serve different purposes.

2. **Evidence constrains both directions**: Setting evidence must set both π (what we know) and λ (what's possible).

3. **Backward flow is essential**: Without λ messages, contrapositive reasoning is impossible.

4. **Follow Pearl exactly**: The original BP algorithm works; simplifications break things.

## Test Result

Before fix:

    ❌ contrapositive/01_not_mortal  got unknown (P=0.5), expected no

After fix:

    ✅ contrapositive/01_not_mortal  P=0.0 (no)