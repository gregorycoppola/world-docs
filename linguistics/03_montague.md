# Montague Grammar

## The Radical Claim

Richard Montague (1970) made a bold claim:

> "There is in my opinion no important theoretical difference between natural languages and the artificial languages of logicians."

He showed that natural language could be given rigorous formal semantics, just like predicate logic.

## Key Ideas

### Compositionality

The **Principle of Compositionality** (attributed to Frege):

> The meaning of a complex expression is determined by the meanings of its parts and the way they are combined.

This is also called **Frege's Principle**.

Montague made this precise: for every syntactic rule combining expressions A and B, there's a corresponding semantic rule combining their meanings.

### Meaning as Function

Montague treated meanings as functions:

- Nouns: Functions from entities to truth values
  - "dog" = λx. dog(x)
  
- Adjectives: Functions that modify noun meanings
  - "big" = λP.λx. big(x) ∧ P(x)
  
- Verbs: Functions from arguments to propositions
  - "runs" = λx. run(x)
  - "loves" = λy.λx. love(x, y)

### Type Theory

Every expression has a type:

- e: entities
- t: truth values
- ⟨e,t⟩: functions from entities to truth values (predicates)
- ⟨e,⟨e,t⟩⟩: functions from entities to predicates (transitive verbs)

This ensures compositional combination is well-defined.

### Quantifiers

Montague's treatment of quantifiers was particularly influential.

"Every man runs":

    every = λP.λQ. ∀x[P(x) → Q(x)]
    man = λx. man(x)
    runs = λx. run(x)
    
    every(man)(runs) = ∀x[man(x) → run(x)]

The quantifier "every" takes two predicates and produces a proposition.

### Intensionality

Montague handled intensional contexts (beliefs, possibilities):

    "John believes that Mary is happy"

The embedded clause denotes not a truth value but a **proposition** (function from possible worlds to truth values).

## Example Derivation

Sentence: "Every man runs"

Syntax:

    S
    ├── NP: "every man"
    │   ├── Det: "every"
    │   └── N: "man"
    └── VP: "runs"

Semantics:

    ⟦man⟧ = λx. man(x)
    ⟦every⟧ = λP.λQ. ∀x[P(x) → Q(x)]
    ⟦every man⟧ = ⟦every⟧(⟦man⟧) = λQ. ∀x[man(x) → Q(x)]
    ⟦runs⟧ = λx. run(x)
    ⟦every man runs⟧ = ⟦every man⟧(⟦runs⟧) = ∀x[man(x) → run(x)]

Each step is compositional — no magic, just function application.

## Relevance to QBBN

### Compositional Semantics

Our logical forms are built compositionally:

    "John loves Mary"
    → love(agent: john, patient: mary)

The predicate "love" combines with its arguments to form a proposition.

### Type System

Our type system (e, s, predicates) echoes Montague's:

    e = entity
    s = sentence/proposition
    predicate{role: type, ...} = function type

### Quantification

Our rules handle universal quantification:

    always [x:e]: man(x) → mortal(x)

This is essentially ∀x[man(x) → mortal(x)] with a modal wrapper.

## Limitations

Montague grammar is elegant but:

1. **Complex**: The λ-calculus is powerful but verbose
2. **Fragile**: Small changes break derivations
3. **Limited coverage**: Hard to scale to full language

This motivates CCG (Steedman), which provides a more tractable syntax-semantics interface.

## Key References

- Montague, R. (1970). "English as a Formal Language"
- Montague, R. (1973). "The Proper Treatment of Quantification in Ordinary English"
- Dowty, D., Wall, R., & Peters, S. (1981). *Introduction to Montague Semantics*