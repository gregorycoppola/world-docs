# Coreference Resolution

## Overview

Coreference resolution identifies when different expressions in text refer to the same entity.

Example:

    "John went to the store. He bought milk."
    
    "John" and "He" refer to the same entity.

## Types of Referring Expressions

### Pronouns

    he, she, it, they, him, her, them, his, hers, its, their

### Definite NPs

    "the president", "the company", "the aforementioned issue"

### Proper Names

    "John", "Mary", "Anthropic", "Paris"

### Demonstratives

    "this", "that", "these", "those"

### Zero Anaphora

    "John went home and [∅] ate dinner."
    
    The subject of "ate" is implicit.

## Coreference vs. Anaphora

**Anaphora**: A later expression refers back to an earlier one.

    "John loves his dog." (his → John)

**Cataphora**: An earlier expression refers forward.

    "Before he left, John fed the cat." (he → John)

**Coreference**: Any expressions that co-refer, regardless of order.

## Example

    "Mary said she would bring her laptop. The CEO needs it for the presentation."
    
    Coreference chains:
    - [Mary, she, her, The CEO]
    - [her laptop, it]

## Approaches

### Rule-Based (Hobbs Algorithm)

1. Search parse tree for antecedents
2. Apply syntactic constraints
3. Prefer closer, more salient entities

### Statistical

Features:
- Distance between mentions
- Syntactic position
- Gender/number agreement
- Semantic compatibility

Models: Maximum entropy, SVM, neural

### Neural End-to-End

Lee et al. (2017): End-to-end neural coreference resolution

1. Embed all spans
2. Score each span as a mention
3. Score pairs of mentions for coreference
4. Cluster mentions into entities

State-of-the-art: ~80% F1 on OntoNotes.

## Constraints

### Agreement

Pronouns must agree in gender and number:

    *"Mary said he would come." (gender mismatch)
    *"The dogs ate its food." (number mismatch)

### Binding Theory

Chomsky's binding constraints:

- Reflexives must be bound locally: "John hurt himself"
- Pronouns must be free locally: "John thinks he is smart"
- R-expressions must be free: *"He thinks John is smart" (if he=John)

### Semantic Compatibility

    *"The table said it was hungry."
    
    Tables can't speak or be hungry.

## Relevance to QBBN

Coreference is essential for logical form construction:

    Input: "Socrates was a philosopher. He was Greek."
    
    Without coreference:
      philosopher(socrates)
      greek(he)  # Who is "he"?
    
    With coreference:
      philosopher(socrates)
      greek(socrates)  # Resolved

### Entity Tracking

Our system maintains an entity inventory:

    Entities:
      e1: socrates
      e2: plato
    
    Mentions:
      "Socrates" → e1
      "He" → e1
      "the philosopher" → e1 (in context)

### Cross-Sentence Reasoning

Coreference enables multi-sentence inference:

    "Socrates was a man. He was mortal."
    
    man(socrates)
    mortal(socrates)  # "He" resolved to socrates
    
    Can verify: man(x) → mortal(x) ✓

## Challenges

### Ambiguity

    "John told Bill that he was wrong."
    
    "he" could be John or Bill.

### World Knowledge

    "The city council refused the demonstrators a permit because they feared violence."
    
    "they" = city council (requires understanding who would fear violence)

### Long-Distance Coreference

Entities mentioned pages apart in a document.

## Tools

**AllenNLP**: Neural coreference

**Stanford CoreNLP**: Rule-based and statistical

**spaCy + neuralcoref**: Fast neural coreference