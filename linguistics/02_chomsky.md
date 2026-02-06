# Chomsky and Generative Grammar

## The Chomskyan Revolution

Before Noam Chomsky, linguistics was primarily descriptive — cataloging the patterns of different languages. Chomsky transformed linguistics into an explanatory science by asking: What is the mental system that allows humans to produce and understand an infinite number of sentences?

## Core Ideas

### Generative Grammar

A grammar is a finite system of rules that generates an infinite set of sentences. The grammar is:
- **Explicit**: Precisely stated rules
- **Generative**: Produces all and only the grammatical sentences
- **Competence-based**: Models the ideal speaker's knowledge, not performance errors

### Deep Structure and Surface Structure

In early Chomskyan theory (Standard Theory, ~1965):
- **Deep structure**: The underlying logical form
- **Surface structure**: What we actually say/hear
- **Transformations**: Rules that map deep to surface

Example: "John was seen by Mary"
- Deep structure: Mary saw John
- Transformation: Passive movement

### Universal Grammar (UG)

Chomsky proposed that humans have an innate language faculty — a Universal Grammar that constrains what languages are possible. This explains:
- How children learn language so quickly
- Why all languages share certain properties
- Why certain patterns never occur in any language

### The Minimalist Program

In later work (~1995), Chomsky stripped the theory down to its essentials:
- No deep structure / surface structure distinction
- Just two interfaces: PF (sound) and LF (meaning)
- Syntax is the optimal solution to connecting these interfaces

The grammar consists of:
- **Merge**: Combine two elements into a larger structure
- **Move**: Displace elements (reanalyzed as internal merge)

## The Two Interfaces

This is the key concept for our purposes. Every sentence has:

**PF (Phonetic Form)**
- Linear order of words
- Prosody, stress patterns
- What you hear or read

**LF (Logical Form)**
- Hierarchical structure
- Scope of quantifiers
- What you understand

Example: "Everyone loves someone"
- PF: the string "everyone loves someone"
- LF (reading 1): ∀x∃y love(x,y) — everyone has someone they love
- LF (reading 2): ∃y∀x love(x,y) — there's someone everyone loves

The syntax determines the possible LF readings. Ambiguity arises when one PF maps to multiple LFs.

## Relevance to Our System

Chomsky established that:

1. **Language has formal structure** — It's not just word associations
2. **Meaning is compositional** — Built from parts via rules
3. **Syntax determines semantic possibilities** — The parse constrains the meaning
4. **There's a level of logical form** — LF is real and computable

Our system takes this seriously. We parse to logical form because Chomsky showed that logical form is a genuine level of linguistic representation, not an artificial imposition.

## Limitations

Chomsky's program has limitations for practical NLP:

1. **Competence vs. performance**: Real language is messy
2. **No statistics**: Classical generative grammar has no probabilities
3. **English-centric**: Much work focused on English transformations
4. **Semantics underdeveloped**: Chomsky cared more about syntax

These limitations motivated the work of Montague (formal semantics) and Steedman (statistical parsing).

## Key References

- Chomsky, N. (1957). *Syntactic Structures*
- Chomsky, N. (1965). *Aspects of the Theory of Syntax*
- Chomsky, N. (1995). *The Minimalist Program*