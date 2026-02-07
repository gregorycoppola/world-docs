# The OR Factor (Ψ_or)

## Purpose

The OR factor combines multiple potential causes of a conclusion. If ANY cause is active, the conclusion tends to be true.

    p ← g₁ ∨ g₂ ∨ ... ∨ gₘ

Each group gᵢ represents one way to derive the conclusion p.

## The Noisy-OR Model

We use the noisy-OR model, which assumes:
1. Each cause independently "tries" to produce the effect
2. The effect occurs if ANY cause succeeds
3. Each cause has a probability of success (determined by weight)

**Formula**:

    P(p=1 | g₁, ..., gₘ) = 1 - ∏ᵢ (1 - wᵢ × gᵢ)

Where:
- gᵢ ∈ {0, 1} is the truth value of group i
- wᵢ = 1 - exp(-weightᵢ) is the "success probability" of cause i

**Intuition**: The conclusion is false only if ALL causes fail to produce it.

## Weight Interpretation

The weight determines how strongly a cause implies the conclusion:

    weight → w = 1 - exp(-weight) → meaning
    
    99.0  →  1.0    →  deterministic (always causes)
    2.3   →  0.9    →  usually causes
    1.4   →  0.75   →  likely causes
    0.7   →  0.5    →  sometimes causes
    -2.3  →  0.1    →  rarely causes (actually computed differently)
    -99.0 →  0.0    →  never causes

For negative weights, we use:

    leak = exp(-weight)  (which is > 1 for negative weights)

This allows inhibitory effects.

## Example

Two rules for "successful":

    Rule 1: smart(x) -> successful(x)     weight = 2.3 (usually)
    Rule 2: lucky(x) -> successful(x)     weight = 0.7 (sometimes)

If smart(john) is true and lucky(john) is false:

    g₁ = 1 (smart rule fires)
    g₂ = 0 (lucky rule doesn't fire)
    
    w₁ = 1 - exp(-2.3) ≈ 0.9
    w₂ = 1 - exp(-0.7) ≈ 0.5
    
    P(successful=1) = 1 - (1 - 0.9×1)(1 - 0.5×0)
                    = 1 - (0.1)(1.0)
                    = 0.9

If both are true:

    P(successful=1) = 1 - (1 - 0.9)(1 - 0.5)
                    = 1 - (0.1)(0.5)
                    = 0.95

Multiple causes increase probability (diminishing returns).

## Forward Message (π)

Computing π(p) from groups:

    prob_not_caused = (1 - base_leak)
    
    for each group gᵢ:
        if weight ≥ 50:  # deterministic
            leak = 0
        else:
            leak = exp(-weight)
        
        prob_not_caused *= (1 - π(gᵢ=1)) + π(gᵢ=1) × leak
    
    π(p=1) = 1 - prob_not_caused
    π(p=0) = prob_not_caused

**base_leak** is a small probability (e.g., 0.001) that the conclusion is true even with no active causes.

## Backward Message (λ)

Computing λ(gᵢ) from conclusion:

For deterministic rules (weight ≥ 50):

    λ(gᵢ=0) = λ(p=0) + λ(p=1)    # g=0 consistent with any p
    λ(gᵢ=1) = λ(p=1)              # g=1 forces p=1

For soft rules:

    leak = exp(-weight)
    
    λ(gᵢ=0) = λ(p=0) + λ(p=1)
    λ(gᵢ=1) = λ(p=0) × leak + λ(p=1)

**Intuition**: If the conclusion must be false (λ(p=1)=0), then:
- For deterministic rules: the group must be false
- For soft rules: the group being true is penalized but not forbidden

## Comparison to Logical OR

Logical OR:

    p = g₁ ∨ g₂
    
    P(p=1 | g₁=1) = 1.0  (certain)
    P(p=1 | g₁=0, g₂=0) = 0.0  (certain)

Noisy-OR with weight=99 is approximately logical OR.

Noisy-OR with weight=2.3:

    P(p=1 | g₁=1) = 0.9  (probable)
    P(p=1 | g₁=0, g₂=0) = base_leak  (unlikely)

## Why Noisy-OR?

1. **Natural for causation**: Multiple independent causes is a common pattern
2. **Computationally tractable**: Factors over 2^n configurations but computable in O(n)
3. **Learnable**: Weights can be learned from data via gradient descent
4. **Interpretable**: Each weight has clear meaning (causal strength)

## Learning Weights

The noisy-OR parameters can be learned by maximum likelihood:

    L = Σ log P(conclusion | premises)

Gradient:

    ∂L/∂wᵢ = (observed - predicted) × premise_value

This is convex optimization when premises are observed.