# First-Order Logic

## What is First-Order Logic?

First-Order Logic (FOL), also called predicate logic, extends propositional logic with:

1. **Objects** (entities in a domain)
2. **Predicates** (properties and relations over objects)
3. **Quantifiers** (∀ for all, ∃ there exists)
4. **Functions** (mappings from objects to objects)

FOL is the standard language of mathematics and formal reasoning.

## Syntax

**Terms**: Refer to objects

    term ::= constant | variable | function(term, ...)

**Atomic formulas**: Predicates applied to terms

    atom ::= predicate(term, ...)

**Formulas**: Built from atoms with connectives and quantifiers

    φ ::= atom | ¬φ | φ ∧ φ | φ ∨ φ | φ → φ | ∀x.φ | ∃x.φ

## Semantics

A **model** M = (D, I) consists of:
- A domain D (set of objects)
- An interpretation I that maps:
  - Constants to objects in D
  - Predicates to relations over D
  - Functions to functions over D

**Variable assignment**: A function g: Variables → D

**Satisfaction**: M, g ⊨ φ means φ is true in model M under assignment g

    M, g ⊨ P(t₁, ..., tₙ) iff (⟦t₁⟧, ..., ⟦tₙ⟧) ∈ I(P)
    M, g ⊨ ∀x.φ iff for all d ∈ D, M, g[x↦d] ⊨ φ
    M, g ⊨ ∃x.φ iff for some d ∈ D, M, g[x↦d] ⊨ φ

## Examples

**Socrates syllogism**:

    ∀x. man(x) → mortal(x)    (All men are mortal)
    man(socrates)              (Socrates is a man)
    ∴ mortal(socrates)         (Therefore Socrates is mortal)

**Transitivity**:

    ∀x,y,z. (greater(x,y) ∧ greater(y,z)) → greater(x,z)

**Existence**:

    ∃x. king(x) ∧ bald(x)     (There exists a bald king)

## Important Concepts

**Free vs. bound variables**: A variable is bound if it's in the scope of a quantifier, free otherwise.

**Sentence**: A formula with no free variables.

**Ground term/formula**: Contains no variables at all.

**Substitution**: φ[t/x] replaces free occurrences of x with t.

## Completeness and Decidability

**Gödel's Completeness Theorem (1930)**: FOL is complete — every valid formula is provable.

**Church-Turing Theorem**: FOL is undecidable — there's no algorithm to determine validity in general.

**Decidable fragments**:
- Propositional logic
- Monadic FOL (unary predicates only)
- Two-variable FOL
- Description logics (restricted quantification)

## Proof Methods

**Natural Deduction**: Gentzen-style rules for each connective and quantifier

**Resolution**: Extended from propositional to first-order
- Requires Skolemization (eliminate ∃) and unification

**Tableaux**: Tree-based proof search

**Sequent Calculus**: Foundational for proof theory

## Relevance to QBBN

Our logical language is inspired by FOL but differs:

| Aspect | Standard FOL | Our Language |
|--------|--------------|--------------|
| Arguments | Positional: P(a, b) | Role-labeled: P(agent: a, patient: b) |
| Quantifiers | Explicit: ∀x, ∃x | Implicit in rules + modals |
| Inference | Theorem proving | Belief propagation |
| Semantics | Model-theoretic | Probabilistic |

**Grounding**: We convert quantified rules to ground instances:

    Rule: always [x:e]: man(theme: x) -> mortal(theme: x)
    Entity: socrates
    Ground: man(theme: socrates) -> mortal(theme: socrates)

This is similar to "propositionalization" in statistical relational learning.

## Key References

- Enderton, H. B. (2001). *A Mathematical Introduction to Logic*
- van Dalen, D. (2013). *Logic and Structure*
- Fitting, M. (1996). *First-Order Logic and Automated Theorem Proving*