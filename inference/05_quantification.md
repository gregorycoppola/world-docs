# Quantification

## Universal Quantification

### In Traditional Logic

Universal quantification asserts something holds for all entities:

    ∀x. man(x) → mortal(x)

"For all x, if x is a man, then x is mortal."

### In QBBN

We handle universal quantification via **grounding**: instantiating the rule for each relevant entity.

**Rule**:

    always [x:e]: man(theme: x) -> mortal(theme: x)

**Entities**: socrates, plato, zeus

**Grounded rules**:

    man(socrates) -> mortal(socrates)
    man(plato) -> mortal(plato)
    man(zeus) -> mortal(zeus)

Each grounded rule becomes a separate path in the factor graph.

### Why Grounding Works

The universal statement "all men are mortal" is equivalent to:

    man(socrates) → mortal(socrates) ∧
    man(plato) → mortal(plato) ∧
    man(zeus) → mortal(zeus) ∧
    ...

For a finite domain, this is just a conjunction of implications.

### Lazy Grounding

We don't ground for ALL entities, only relevant ones:

1. Start with the query
2. Expand backward (what rules conclude this?)
3. Expand forward (what does this enable?)
4. Ground only rules in the expansion

This keeps the factor graph tractable.

## Existential Quantification

### In Traditional Logic

Existential quantification asserts something holds for some entity:

    ∃x. loves(mary, x)

"There exists an x such that Mary loves x."

### Challenge in QBBN

Existential statements are harder because:
1. We don't know which entity satisfies the condition
2. We need to combine evidence across possible witnesses

### Current Approach: Disjunction Over Entities

We treat "exists" as disjunction:

    ∃x. loves(mary, x) ≡ loves(mary, john) ∨ loves(mary, bob) ∨ ...

In QBBN:

    P(∃x. loves(mary, x)) = 1 - ∏ᵢ (1 - P(loves(mary, entityᵢ)))

This is exactly the noisy-OR combination.

### Example

Query: "Does Mary love someone?"

Entities: john, bob, alice

    P(loves(mary, john)) = 0.8
    P(loves(mary, bob)) = 0.3
    P(loves(mary, alice)) = 0.1
    
    P(∃x. loves(mary, x)) = 1 - (1-0.8)(1-0.3)(1-0.1)
                          = 1 - (0.2)(0.7)(0.9)
                          = 1 - 0.126
                          = 0.874

### Limitations

**Open world**: What if there's an entity we don't know about?
- Current approach assumes closed world (all entities are known)
- Unknown entities would require different treatment

**Skolemization**: Traditional FOL uses Skolem functions for nested quantifiers.
- We don't currently handle: ∀x. ∃y. loves(x, y)
- Would require more complex grounding

## Quantified Rules with Multiple Variables

### Binary Relations

Rule with two variables:

    always [x:e, y:e]: parent(x, y) & parent(y, z) -> grandparent(x, z)

**Grounding**: For each triple (a, b, c) of entities:

    parent(a, b) & parent(b, c) -> grandparent(a, c)

**Combinatorial explosion**: O(n³) groundings for ternary rule, O(n^k) for k variables.

### Mitigation

1. **Type constraints**: Restrict variables by type
    
        [x:person, y:person]: ...
    
    Only ground over persons, not all entities.

2. **Query-driven**: Only ground rules relevant to the query.

3. **Sparse evidence**: Most groundings have no evidence, contribute only base rate.

## Modal Quantification

Our modals interact with quantification:

    always [x:e]: bird(x) -> flies(x)     # ∀x. bird(x) → flies(x) with P=1
    usually [x:e]: bird(x) -> flies(x)    # ∀x. bird(x) → flies(x) with P=0.9

The modal applies to each grounded instance:

    P(flies(tweety) | bird(tweety)) = 0.9
    P(flies(polly) | bird(polly)) = 0.9

Each instance is independent (no correlation structure between instances).

## Summary

| Quantifier | QBBN Approach |
|------------|---------------|
| ∀ (universal) | Grounding: instantiate for each entity |
| ∃ (existential) | Disjunction: noisy-OR over entities |
| Modal + ∀ | Grounding with weighted rules |