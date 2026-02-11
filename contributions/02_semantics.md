# Contribution 2: Semantics

## The Problem

What formal language should natural language be translated into? The language must be expressive enough to represent all natural language meanings, precise enough for the inference engine to reason over, and close enough to surface text that parsing is tractable.

## What We Contribute

A typed predicate language with role-labeled arguments, modal quantifiers, and three tiers of expressiveness grounded in Prawitz's natural deduction (1965). We claim this language is sufficient for natural language semantics — the remaining work is lexical (which words map to which predicates and types), not logical (no new type machinery is needed).

### Design Choices

**Role-labeled arguments** rather than positional: `trust(agent: jack, patient: jill)` not `trust(jack, jill)`. This bridges dependency parsing output (which produces labeled grammatical relations) to logical form. The labels are semantic roles from the linguistics tradition (Fillmore, 1968; Dowty, 1991).

**Modal quantifiers** that map natural language hedging to probabilistic weights: `always` → 0.99, `usually` → 0.9, `sometimes` → 0.5. These parameterize the noisy-OR factors in the inference engine, turning Prawitz's deterministic universal elimination into a probabilistic version.

**A lexicon** that separates vocabulary definition from assertion, providing the bridge between the grammar (which operates on surface forms) and the logical language (which operates on typed predicates).

### Three Tiers of Expressiveness

The sufficiency claim rests on three tiers, each grounded in Prawitz:

#### Tier 1: First-Order (Prawitz Chapters I–IV)

Entities, predicates with typed roles, universally quantified Horn clauses, negation.

Examples from test suite:

    man(theme: socrates)
    always [x:e]: man(theme: x) -> mortal(theme: x)
    not mortal(theme: zeus)

This handles: predication, classification, quantified rules, negation, conjunction (AND gates), disjunction (OR/noisy-OR), transitivity, symmetry, identity, causation, spatial and temporal reasoning.

Coverage: 44/44 inference tests pass. This tier covers the vast majority of natural language reasoning.

#### Tier 2: Propositions as Arguments (Prawitz Chapters V–VI)

The `content: s` type allows a predicate to take a full proposition as an argument.

Examples from test suite:

    should(content: bring_umbrella())
    should(content: careful(theme: mary))
    should(content: apologize(agent: o))

The last example is significant: the variable `o` is bound by the outer quantifier and appears inside the embedded proposition. The grounding engine handles variables that cross into embedded sentences.

This tier covers: modal verbs (should, can, must), propositional attitudes (believe, want, know, think), reported speech ("John said that..."), and any construction where a sentence is an argument to a predicate.

Coverage: 3 test cases exercise this tier and pass. Deeper modal reasoning (chaining rules within belief contexts) is future work, but the representational and inferential machinery is in place.

#### Tier 3: Predicate Quantification (Prawitz Chapter V)

Variables that range over predicates rather than entities. Lambda abstraction for building compound predicates.

    is_a(theme: john, class: λx. man(x) & tall(x))
    ∀P: P(john) → P(mary)    ("whatever is true of John is true of Mary")
    ∃P: P(john) & P(mary)    ("John and Mary have something in common")

This tier covers: compound predicates (adjective stacking, relative clauses as predicate modifiers), quantification over properties, and certain definitions.

Coverage: designed and theoretically grounded in Prawitz but not yet tested in inference. For the common cases (compound predicates like "tall man"), first-order conjunction in rule premises handles it without needing lambda — the relative clause tests demonstrate this. The cases that genuinely require second-order quantification (existential predicate queries, predicate-level generalization) are future work.

### The Sufficiency Argument

Every natural language sentence can be decomposed into:

- Entities (people, places, things)
- Properties and relations over entities (predicates with typed roles) — Tier 1
- Quantification over entities (all, some, no) — Tier 1
- Propositions embedded under modal or attitudinal predicates (believe, should, can) — Tier 2
- Properties as arguments, compound predicates (the tall man, whatever John is) — Tier 3

This decomposition follows Montague (1970, 1973) and is formalized by Prawitz (1965) across first-order (Chapters I–IV), second-order (Chapter V), and modal logic (Chapter VI). Prawitz proved normal form theorems for all three levels, meaning the proof theory is well-understood. Our system implements these levels as a probabilistic factor graph — replacing {0, 1} truth values with [0, 1] belief values while preserving the inference structure.

The remaining work is lexical: determining which English words map to which predicates, which roles they take, and what their types are. No new logical machinery is needed.