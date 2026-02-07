# Dependency Parsing

## Overview

Dependency parsing identifies grammatical relations between words. Unlike phrase-structure parsing (which builds trees of constituents), dependency parsing connects words directly.

## Dependency Relations

A dependency is a directed relation from a **head** to a **dependent**:

    head ──relation──→ dependent

Common relations (Universal Dependencies):

| Relation | Meaning | Example |
|----------|---------|---------|
| nsubj | nominal subject | "John runs" — runs→John |
| dobj | direct object | "sees Mary" — sees→Mary |
| iobj | indirect object | "gave him" — gave→him |
| nmod | nominal modifier | "cup of tea" — cup→tea |
| amod | adjectival modifier | "big dog" — dog→big |
| det | determiner | "the cat" — cat→the |
| case | case marking | "to Mary" — Mary→to |
| root | root of sentence | ROOT→main_verb |

## Example

Sentence: "John gave Mary a book"

Dependencies:

    ROOT
     │
     └──root──→ gave
                 ├──nsubj──→ John
                 ├──iobj──→ Mary
                 └──dobj──→ book
                            └──det──→ a

As relations:

    root(ROOT, gave)
    nsubj(gave, John)
    iobj(gave, Mary)
    dobj(gave, book)
    det(book, a)

## Parsing Algorithms

### Transition-Based Parsing

Build the parse incrementally with actions:

- SHIFT: Move word from buffer to stack
- LEFT-ARC(r): Create relation from stack top to second
- RIGHT-ARC(r): Create relation from second to stack top

Neural models (Kiperwasser & Goldberg, 2016) score actions.

### Graph-Based Parsing

Score all possible edges, find best tree:

    score(tree) = Σ_{(h,d,r) ∈ tree} score(h, d, r)

Find maximum spanning tree (MST algorithms).

## Universal Dependencies

The Universal Dependencies (UD) project provides:

- Consistent annotation across languages
- Treebanks for 100+ languages
- Standard relation inventory

This enables cross-lingual transfer and comparison.

## Relevance to QBBN

Dependency relations map to semantic roles:

    nsubj → often agent
    dobj → often patient/theme
    iobj → often recipient

Our pipeline uses dependencies as an intermediate representation:

    Text → Dependencies → Semantic Roles → Logical Form

Example:

    "John loves Mary"
    
    Dependencies:
      nsubj(loves, John)
      dobj(loves, Mary)
    
    Semantic roles:
      love(agent: John, patient: Mary)
    
    Logical form:
      love(agent: john, patient: mary)

## Challenges

### Ambiguity

"I saw the man with the telescope"

    Parse 1: saw with telescope (I used the telescope)
    Parse 2: man with telescope (man had the telescope)

Different dependency structures, different meanings.

### Long-Distance Dependencies

"What did John say that Mary bought?"

The "what" is semantically the object of "bought" but syntactically far away.

### Coordination

"John and Mary went to the store and bought milk"

Complex coordination requires careful handling.

## Tools

**spaCy**: Fast, accurate dependency parsing

    doc = nlp("John loves Mary")
    for token in doc:
        print(f"{token.text} <--{token.dep_}-- {token.head.text}")

**Stanza**: Stanford's neural parser

**Trankit**: Transformer-based multilingual parsing