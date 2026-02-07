# The AND Factor (Ψ_and)

## Purpose

The AND factor computes conjunction: a group is true iff ALL its premises are true.

    g = p₁ ∧ p₂ ∧ ... ∧ pₙ

This represents the premises of a rule that must all hold for the rule to fire.

## Definition

The AND factor is deterministic:

    Ψ_and(g | p₁, ..., pₙ) = {
        1  if g = (p₁ ∧ p₂ ∧ ... ∧ pₙ)
        0  otherwise
    }

Equivalently:

    Ψ_and(g=1 | p₁, ..., pₙ) = ∏ᵢ pᵢ       (all must be 1)
    Ψ_and(g=0 | p₁, ..., pₙ) = 1 - ∏ᵢ pᵢ   (at least one is 0)

## Example

Rule: "Dating requires mutual attraction"

    likes(jack, jill) & likes(jill, jack) -> dating(jack, jill)

The AND factor:

    g₁ = likes(jack, jill) ∧ likes(jill, jack)
    
    Ψ_and(g₁ | p₁, p₂) where:
        p₁ = likes(jack, jill)
        p₂ = likes(jill, jack)

Truth table:

    p₁  p₂  g₁
    0   0   0
    0   1   0
    1   0   0
    1   1   1

## Forward Message (π)

Computing π(g) from premises:

    π(g=1) = ∏ᵢ π(pᵢ=1)
    π(g=0) = 1 - π(g=1)

**Intuition**: The probability that the group is true equals the probability that all premises are true (assuming independence).

**Example**:

    π(p₁=1) = 0.8
    π(p₂=1) = 0.7
    
    π(g=1) = 0.8 × 0.7 = 0.56
    π(g=0) = 1 - 0.56 = 0.44

## Backward Message (λ)

Computing λ(pᵢ) from group:

For each premise pᵢ, we need to consider how pᵢ affects g given the other premises.

    other_prob = ∏_{j≠i} π(pⱼ=1)
    
    λ(pᵢ=1) = other_prob × λ(g=1) + (1 - other_prob) × λ(g=0)
    λ(pᵢ=0) = λ(g=0)

**Intuition**:
- If pᵢ=0, then g=0 regardless of others, so λ(pᵢ=0) = λ(g=0)
- If pᵢ=1, then g depends on others: g=1 if all others true, g=0 otherwise

**Example** (two premises, other premise has π=0.7):

    λ(g=1) = 0.0  (group must be false)
    λ(g=0) = 1.0
    
    other_prob = 0.7
    
    λ(p₁=1) = 0.7 × 0.0 + 0.3 × 1.0 = 0.3
    λ(p₁=0) = 1.0

This says: if the group must be false, p₁ being false is more consistent than p₁ being true.

## Special Case: Single Premise

When there's only one premise:

    g = p₁
    
    π(g) = π(p₁)
    λ(p₁) = λ(g)

The AND factor just passes messages through unchanged.

## Why Deterministic?

The AND factor is always deterministic (not learned) because:

1. **Logical correctness**: Conjunction has fixed semantics
2. **Feature creation**: AND creates higher-level features for OR to learn over
3. **Interpretability**: We know exactly what the group represents

The learning happens in the OR factor, which combines groups with learned weights.