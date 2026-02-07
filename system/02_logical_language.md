# The Logical Language

## Overview

The logical language is the intermediate representation between natural language and the factor graph. It's designed to be:

- **Precise**: Unambiguous semantics
- **Compositional**: Meaning built from parts
- **Role-based**: Arguments labeled with semantic roles
- **Modal**: Rules have strength (always, usually, sometimes)

## Tiered Architecture

### Tier 1: Atoms

The basic building blocks:

**Type**: A category of entity

    e       # generic entity
    person  # a person
    city    # a city
    loc     # a location

**RoleLabel**: A semantic role

    agent    # doer of action
    patient  # affected entity
    theme    # entity in state/motion
    goal     # destination
    source   # origin

**Entity**: A specific individual

    socrates
    paris
    john

### Tier 2: Simple Compositions

Combining atoms:

**Constant**: An entity with its type

    socrates : e
    paris : city

**Variable**: A bound variable in rules

    x : e
    y : person

### Tier 3: Predicates

The core structure:

**Predicate**: A relation with role-labeled arguments

    man(theme: socrates)
    love(agent: john, patient: mary)
    give(agent: john, recipient: mary, theme: book)

Each predicate has:
- A function name (e.g., "man", "love", "give")
- Zero or more role-argument pairs
- Optional negation

## Grammar

### Document Structure

    document := predicate_decl* entity_decl* sentence* query

A document has four sections (in order):
1. Predicate declarations
2. Entity declarations
3. Sentences (facts and rules)
4. Query

### Predicate Declarations

    predicate_decl := "predicate" NAME "{" role_list "}"
    role_list      := ε | role ("," role)*
    role           := NAME ":" TYPE

Examples:

    predicate man {theme: e}
    predicate love {agent: e, patient: e}
    predicate think {agent: e, content: s}
    predicate transitive {relation: [e, e]}

Special types:
- `e` — entity
- `s` — sentence (nested predicate)
- `[e, e]` — binary relation (predicate reference)

### Entity Declarations

    entity_decl := "entity" NAME ":" TYPE

Examples:

    entity socrates : e
    entity paris : city
    entity france : country

### Facts (Propositions)

    proposition := ["not"] NAME "(" arg_list ")"
    arg_list    := arg ("," arg)*
    arg         := NAME ":" value
    value       := NAME | predicate

Examples:

    man(theme: socrates)
    love(agent: john, patient: mary)
    not mortal(theme: zeus)
    think(agent: john, content: rain())

### Rules

    rule         := [MODAL] [var_bindings] ":" premises "->" conclusion
    var_bindings := "[" var_decl ("," var_decl)* "]"
    var_decl     := NAME ":" TYPE
    premises     := predicate ("&" predicate)*
    conclusion   := predicate

Examples:

    always [x:e]: man(theme: x) -> mortal(theme: x)
    usually [x:e, y:e]: love(agent: x, patient: y) -> love(agent: y, patient: x)
    always [x:e, y:e, z:e]: parent(agent: x, patient: y) & parent(agent: y, patient: z) -> grandparent(agent: x, patient: z)

### Queries

    query := "?" predicate

Examples:

    ? mortal(theme: socrates)
    ? love(agent: mary, patient: john)

## Modal Quantifiers

Modals express the strength of a rule:

| Modal | Weight | P(conclusion \| premises) |
|-------|--------|---------------------------|
| `always` | 99.0 | ≈ 1.0 (deterministic) |
| `usually` | 2.3 | ≈ 0.9 |
| `likely` | 1.4 | ≈ 0.75 |
| `sometimes` | 0.7 | ≈ 0.5 |
| `rarely` | -2.3 | ≈ 0.1 |
| `never` | -99.0 | ≈ 0.0 (blocks) |

The weight w relates to probability via the noisy-OR model:

    P(conclusion | premise=true) = 1 - exp(-w)

## Complete Example

    # Predicate declarations
    predicate man {theme: e}
    predicate mortal {theme: e}
    predicate god {theme: e}
    
    # Entity declarations
    entity socrates : e
    entity zeus : e
    
    # Facts
    man(theme: socrates)
    god(theme: zeus)
    not mortal(theme: zeus)
    
    # Rules
    always [x:e]: man(theme: x) -> mortal(theme: x)
    never [x:e]: god(theme: x) -> mortal(theme: x)
    
    # Query
    ? mortal(theme: socrates)

## Design Decisions

### Role Labels (vs. Positional Arguments)

Traditional FOL: `love(john, mary)`
Our language: `love(agent: john, patient: mary)`

**Why?**
1. Closer to dependency parse output
2. Order-independent
3. Self-documenting
4. Matches semantic role labeling

### Implicit Quantification

No explicit ∀ or ∃. Instead:
- Variables in rules are universally quantified
- Modal strength modulates the universal

    always [x:e]: P(x) -> Q(x)

means: ∀x. P(x) → Q(x) with probability 1.0

### Negation

Negation is syntactic (`not pred(...)`) and handled via:
1. Negation factors (Ψ_neg) linking P and ¬P
2. Constraint: P(X) + P(¬X) = 1

### Nested Predicates

For propositional attitudes:

    think(agent: john, content: mortal(theme: socrates))

The nested predicate is typed as `s` (sentence).

## Comparison to Other Languages

| Feature | Our Language | Prolog | FOL | Description Logic |
|---------|--------------|--------|-----|-------------------|
| Arguments | Role-labeled | Positional | Positional | Role-labeled |
| Quantifiers | Implicit + modal | Implicit | Explicit | Restricted |
| Negation | Explicit | NAF | Classical | Open/closed world |
| Probability | Noisy-OR | None (ProbLog adds) | None | None |