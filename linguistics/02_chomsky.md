# Chomsky and Generative Grammar

## The Revolution

Before Chomsky, linguistics was largely descriptive — cataloging patterns in languages. Chomsky (1957) proposed that language is **generative**: a finite set of rules that can produce an infinite set of sentences.

## Key Ideas

### Generative Grammar

A grammar is a formal system that generates exactly the grammatical sentences of a language:

    S → NP VP
    NP → Det N
    VP → V NP
    
    Det → "the" | "a"
    N → "cat" | "dog"
    V → "chased" | "saw"

This generates: "the cat chased a dog", "a dog saw the cat", etc.

### Deep Structure vs. Surface Structure

Chomsky proposed that sentences have two levels:

- **Deep structure**: The underlying logical form
- **Surface structure**: What we actually say

Transformations map deep to surface:

    Deep: [John [past see] Mary]
    Surface: "John saw Mary"
    
    Deep: [Mary [past see] by John]
    Surface: "Mary was seen by John"

The same deep structure can yield different surface forms.

### Competence vs. Performance

- **Competence**: What you know about language (the grammar)
- **Performance**: What you actually do (subject to memory limits, errors, etc.)

Linguistics studies competence — the idealized system.

### Universal Grammar

Chomsky's most ambitious claim: all human languages share a common underlying structure (Universal Grammar or UG). Children don't learn language from scratch — they have innate knowledge of possible grammars.

## Evolution of the Theory

### Standard Theory (1965)

Aspects of the Theory of Syntax introduced:
- Phrase structure rules
- Transformations
- Deep/surface structure distinction

### Government and Binding (1981)

Replaced construction-specific rules with general principles:
- X-bar theory (all phrases have the same structure)
- Theta theory (argument structure)
- Case theory
- Binding theory (pronouns and anaphora)

### Minimalism (1995)

Stripped the theory to essentials:
- Merge: Combine two elements
- Move: Displace an element
- Interface conditions: PF and LF

The grammar is as simple as possible, with complexity arising from interface requirements.

## Relevance to QBBN

We adopt several Chomskyan insights:

### Hierarchical Structure

Language isn't flat. "The big dog" is a unit (NP) that functions as a whole:

    [S [NP The big dog] [VP chased [NP the cat]]]

Our parsing respects this structure.

### The LF Interface

Chomsky's LF (Logical Form) is where semantic interpretation happens. Our logical form serves the same purpose — it's the representation that inference operates on.

### Argument Structure

Verbs have argument structure — they require certain participants:

    chase(agent, patient)
    give(agent, recipient, theme)

This maps directly to our predicate definitions.

## Limitations for Our Purposes

Chomskyan syntax focuses on structural well-formedness, not meaning. For semantic representation, we turn to Montague.

## Key References

- Chomsky, N. (1957). *Syntactic Structures*
- Chomsky, N. (1965). *Aspects of the Theory of Syntax*
- Chomsky, N. (1995). *The Minimalist Program*