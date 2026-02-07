# Factor Graph Construction

## Overview

The factor graph is the runtime representation for inference. It's built from:
1. A query (what we want to know)
2. A knowledge base (facts and rules)
3. An entity inventory (what entities exist)

The graph has a specific bipartite structure that enables efficient belief propagation.

## Graph Structure

The computation always flows through this pattern:

    Propositions (p) → AND factors (Ψ_and) → Groups (g) → OR factors (Ψ_or) → Propositions (p)

Even single-premise rules go through an AND gate (which just passes through).

## Node Types

### Propositions (p)

Grounded predicates that are either true or false.

    p1 = "man(theme: socrates)"
    p2 = "mortal(theme: socrates)"
    p3 = "not mortal(theme: zeus)"

Each proposition has:
- A unique ID
- A formula (the grounded predicate)
- An optional evidence value (True/False)
- A belief (probability distribution)

### Groups (g)

Conjunctions of propositions — the premises of a grounded rule.

    g1 = "group(p1)"           # single premise
    g2 = "group(p1, p3)"       # multiple premises (AND)

Each group has:
- A unique ID
- A list of premise proposition IDs
- A belief (probability distribution)

## Factor Types

### Ψ_and (AND Factor)

Deterministic conjunction: g = p₁ ∧ p₂ ∧ ... ∧ pₙ

    Ψ_and(g=1 | p₁, ..., pₙ) = 1 if all pᵢ=1, else 0
    Ψ_and(g=0 | p₁, ..., pₙ) = 1 if any pᵢ=0, else 0

The group is true iff ALL premises are true.

### Ψ_or (OR Factor)

Noisy-OR disjunction: p is caused by any of its groups.

    Ψ_or(p=1 | g₁, ..., gₘ) = 1 - ∏ᵢ (1 - wᵢ × gᵢ)

Where wᵢ is the weight of the rule that created group gᵢ.

For deterministic rules (weight=99): if any group is true, conclusion is true.
For soft rules: each group contributes probabilistically.

### Ψ_neg (Negation Factor)

Deterministic constraint: P(p) + P(¬p) = 1

    Ψ_neg(p, ¬p) = 1 if p + ¬p = 1, else 0

This links a proposition to its negation, enabling contrapositive reasoning.

## Graph Construction Algorithm

### Step 1: Initialize with Query

Start with the query proposition and add it to the expansion queue.

### Step 2: Expand Forward and Backward

For each proposition to expand:

**Backward**: Find rules that conclude this proposition, add their premises.

**Forward**: Find rules that have this proposition as a premise, add their conclusions.

### Step 3: Add Grounded Rules

For each relevant rule:
- Create a group for the premises
- Create an AND factor connecting premises to the group
- Register the group as a cause of the conclusion

### Step 4: Build OR Factors

After all rules are processed, create OR factors that connect groups to their conclusions with appropriate weights.

### Step 5: Build Negation Factors

Link propositions to their negations (e.g., "mortal(zeus)" ↔ "not mortal(zeus)").

### Step 6: Set Evidence

Mark propositions as true or false based on known facts.

## Example: Contrapositive

**Input**:

    man(theme: zeus) -> mortal(theme: zeus)
    not mortal(theme: zeus)  # evidence
    ? man(theme: zeus)

**Graph**:

    p1: man(zeus)
    p2: mortal(zeus)         [evidence: False]
    p3: not mortal(zeus)     [evidence: True]
    g1: group(p1)
    
    and1: p1 → g1
    or1: g1 → p2
    neg1: p2 ↔ p3

**Inference**:
1. p3 is evidence=True, so p2 must be False (via neg1)
2. λ(p2=1) = 0 propagates backward through or1
3. λ(g1=1) = 0 (group must be false)
4. λ(p1=1) = 0 (premise must be false via and1)
5. Result: P(man(zeus)) = 0

## Complexity

- **Propositions**: O(entities × predicates)
- **Groups**: O(rules × entity^arity)
- **Factors**: O(groups + propositions)

For bounded arity rules and reasonable entity counts, this is tractable.