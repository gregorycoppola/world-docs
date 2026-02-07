# Steedman and CCG

## The Bridge

Combinatory Categorial Grammar (CCG), developed by Mark Steedman, provides a **transparent interface** between syntax and semantics. Every syntactic operation has a direct semantic counterpart.

## Key Ideas

### Categories

Every word has a **category** that specifies:
1. What it is
2. What it needs

Examples:

    john := NP                     # A noun phrase
    runs := S\NP                   # Needs NP on left, gives S
    loves := (S\NP)/NP             # Needs NP on right, then NP on left
    the := NP/N                    # Needs N on right, gives NP
    big := N/N                     # Needs N on right, gives N

The slash indicates direction:
- X/Y: needs Y on the right
- X\Y: needs Y on the left

### Combinatory Rules

**Forward Application (>)**:

    X/Y  Y  →  X
    
    "the" "dog" → NP
    NP/N   N

**Backward Application (<)**:

    Y  X\Y  →  X
    
    "John" "runs" → S
    NP     S\NP

**Forward Composition (>B)**:

    X/Y  Y/Z  →  X/Z

**Backward Composition (<B)**:

    Y\Z  X\Y  →  X\Z

**Type Raising**:

    X  →  T/(T\X)
    
    "John" can become S/(S\NP)
    (something that needs a verb phrase to make a sentence)

### Semantic Composition

Each syntactic rule has a semantic counterpart:

    Syntax:  X/Y : f    Y : a    →    X : f(a)
    
    "loves" "Mary"
    (S\NP)/NP : λy.λx.love(x,y)    NP : mary
    →
    S\NP : λx.love(x, mary)

The semantics composes by function application, just like the syntax.

### Example: "John loves Mary"

Lexicon:

    John  := NP : john
    loves := (S\NP)/NP : λy.λx.love(x,y)
    Mary  := NP : mary

Derivation:

    John          loves              Mary
    NP:john    (S\NP)/NP:λy.λx.love(x,y)    NP:mary
                        ─────────────────────────────>  (forward application)
                           S\NP : λx.love(x,mary)
    ──────────────────────────────────────────────────<  (backward application)
                    S : love(john,mary)

The result is both a syntactic parse (S) and a semantic form (love(john,mary)).

### Handling Long-Distance Dependencies

CCG elegantly handles extraction and long-distance dependencies:

    "the book that John read"
    
    that := (N\N)/(S/NP)  # Relative pronoun
    
    "John read" can type-raise and compose to become S/NP
    Then "that" applies to give N\N (noun modifier)

## Relevance to QBBN

### Transparent Syntax-Semantics

CCG's key insight — syntax and semantics compose in parallel — aligns with our pipeline:

    Syntactic parse → Semantic roles → Logical form

Each stage preserves compositional structure.

### Category as Type

CCG categories are essentially types:

    (S\NP)/NP ≈ function from NP to (function from NP to S)
    
    This is like: e → e → t

Our predicate types follow similar logic.

### Practical Parsing

CCG parsers (like C&C, EasyCCG, depccg) produce semantic representations directly. This is useful for our pipeline.

## Comparison

| Approach | Syntax-Semantics | Practicality |
|----------|------------------|--------------|
| Chomsky | Separate modules | Theory-focused |
| Montague | Compositional | Complex |
| CCG | Transparent parallel | Tractable |

## Key References

- Steedman, M. (2000). *The Syntactic Process*
- Steedman, M., & Baldridge, J. (2011). "Combinatory Categorial Grammar"
- Clark, S., & Curran, J. (2007). "Wide-Coverage Efficient Statistical Parsing with CCG"