---
title: QBBN Forward Expansion and Negation Factors
kind: design
repo: lever
---

# QBBN Forward Expansion and Negation Factors

## Overview

Two related changes to enable contrapositive reasoning in QBBN:

1. **Forward expansion** — when building the graph, also follow rules where the query appears as a premise (not just as a conclusion)
2. **Negation factors** — connect `P` and `not P` with a trivial Ψ_neg factor that enforces `P(not X) = 1 - P(X)`

These work together: forward expansion builds the graph structure needed for constraints to flow, and negation factors let negative evidence propagate.

## Graph Expansion

### Backward Expansion (existing)

**Goal**: Find what could *cause* this proposition to be true.

**Search**: Look for rules where this proposition is the **conclusion**.

```
? mortal(zeus)

Find rules: ??? -> mortal(x)
Found: man(x) -> mortal(x)
Add: man(zeus) to the graph
```

Answers: "What premises would make `mortal(zeus)` true?"

### Forward Expansion (new)

**Goal**: Find what this proposition being true would *cause*.

**Search**: Look for rules where this proposition is a **premise**.

```
? man(zeus)

Find rules: man(x) -> ???
Found: man(x) -> mortal(x)
Add: mortal(zeus) to the graph
```

Answers: "If `man(zeus)` were true, what would follow?"

### Combined Expansion

The new `from_query()` does both:

1. Start with query proposition
2. BFS loop — for each proposition:
   - **Backward**: find rules where it's the conclusion, add premises
   - **Forward**: find rules where it's a premise, add conclusions
   - Queue all newly added propositions
3. Mark facts as evidence
4. Build Ψ_or factors

This creates a more complete local graph around the query.

## Negation Factors

### The Problem

Currently, positive and negative propositions are unconnected:

```
"mortal(theme: zeus)"       # proposition A
"not mortal(theme: zeus)"   # proposition B, no relation to A
```

BP has no way to know these are logically related.

### The Solution: Ψ_neg

A deterministic factor connecting `P` and `not P`:

```
P ←——[Ψ_neg]——→ not P
```

Factor table:

| P | not P | Ψ_neg |
|---|-------|-------|
| 0 | 0     | 0     |
| 0 | 1     | 1     |
| 1 | 0     | 1     |
| 1 | 1     | 0     |

Message passing is trivial:

```python
P(not X) = 1 - P(X)
```

No weights, no training. Same complexity class as Ψ_and.

### When to Create Ψ_neg

Only when both forms appear in the graph. If the KB mentions `not mortal(zeus)` and the rules mention `mortal(zeus)`, connect them.

Implementation: maintain a mapping from positive formulas to their proposition IDs. When adding a proposition, check if its negation (or positive form) already exists. If so, add Ψ_neg between them.

## How They Work Together

Example:
```
not mortal(zeus)           # fact
man(x) -> mortal(x)        # rule  
? man(zeus)                # query
```

### Graph Construction

1. Add `man(zeus)` (query)
2. Backward from `man(zeus)`: no rules conclude `man(...)`
3. Forward from `man(zeus)`: found `man(x) -> mortal(x)`
   - Add `mortal(zeus)`
   - Add group g1, Ψ_and connecting `man(zeus)` → g1
   - Later: Ψ_or connecting g1 → `mortal(zeus)`
4. Process fact `not mortal(zeus)`:
   - Add `not mortal(zeus)` with evidence=True
   - `mortal(zeus)` already exists → add Ψ_neg between them

Resulting graph:
```
man(zeus) --[Ψ_and]--> g1 --[Ψ_or]--> mortal(zeus) --[Ψ_neg]--> not mortal(zeus)
                                                                        |
                                                                  [Evidence=True]
```

### BP Propagation

1. `not mortal(zeus)` = 1.0 (evidence)
2. Ψ_neg propagates → `mortal(zeus)` = 0.0
3. Ψ_or backward → g1 = 0.0
4. Ψ_and backward → `man(zeus)` = 0.0

Result: query returns P=0.0 → answer "no"

## Implementation Summary

### New Factor Type

```python
class FactorType(Enum):
    AND = "and"
    OR = "or"
    NEG = "neg"
```

### Graph Construction (`from_query`)

```python
# Build indices
clauses_by_conclusion: dict[str, list]  # existing
clauses_by_premise: dict[str, list]     # new

# BFS expansion
for formula in to_expand:
    # Backward (existing)
    for clause in clauses_by_conclusion.get(formula, []):
        add_grounded_rule(...)
        queue premises
    
    # Forward (new)
    for clause in clauses_by_premise.get(formula, []):
        add_grounded_rule(...)
        queue conclusion

# Connect negations (new)
for formula in all_formulas:
    negated = get_negated_formula(formula)
    if negated in formula_to_id:
        add_neg_factor(formula, negated)
```

### BP Update (`qbbn_bp.py`)

```python
def _update_neg_factor(graph, factor, messages_to_var):
    input_var = graph.variables.get(factor.input_ids[0])
    output_prob = 1.0 - input_var.prob
    messages_to_var[f"{factor.id}->{factor.output_id}"] = output_prob
```

### Helper Functions

```python
def get_positive_formula(formula: str) -> str:
    if formula.startswith("not "):
        return formula[4:]
    return formula

def is_negated(formula: str) -> bool:
    return formula.startswith("not ")
```

## Test Coverage

This fixes `contrapositive/01_not_mortal`:
- Current: P=0.5 (unknown)
- After: P=0.0 (no)
