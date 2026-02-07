# System Overview

## The Two Interfaces

Our system operates at the boundary between two representations:

1. **Text** — Natural language (English)
2. **Logical Form** — Structured predicates, rules, and queries

This mirrors Chomsky's insight about the two interfaces of language:
- **PF (Phonetic Form)** — The surface realization
- **LF (Logical Form)** — The semantic interpretation

Our system makes this operational: we parse text to logical form, then reason over it.

## Architecture

    ┌─────────────────────────────────────────────────────────────┐
    │                        INPUT                                │
    │                    (English text)                           │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                    SYNTAX LAYERS                            │
    │    Tokenize → Verbs → Arguments → Clauses                   │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                   SEMANTIC LAYERS                           │
    │    Entities → Coreference → Lexicon → Logic                 │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                    LOGICAL FORM                             │
    │           predicates, entities, rules, queries              │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                      GROUNDING                              │
    │              instantiate rules for entities                 │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                    FACTOR GRAPH                             │
    │         propositions, groups, AND/OR/NEG factors            │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                  BELIEF PROPAGATION                         │
    │                    π/λ messages                             │
    └─────────────────────────────────────────────────────────────┘
                                │
                                ▼
    ┌─────────────────────────────────────────────────────────────┐
    │                       OUTPUT                                │
    │              P(query) ∈ [0, 1] + explanation                │
    └─────────────────────────────────────────────────────────────┘

## The Three Languages

The system involves three distinct languages:

### 1. Natural Language (English)

The input language. Ambiguous, implicit, variable.

    Socrates is a man. All men are mortal. Is Socrates mortal?

### 2. Logical Language

The intermediate representation. Precise, explicit, compositional.

    predicate man {theme: e}
    predicate mortal {theme: e}
    
    entity socrates : e
    
    man(theme: socrates)
    always [x:e]: man(theme: x) -> mortal(theme: x)
    
    ? mortal(theme: socrates)

### 3. Graphical Language

The inference representation. Nodes, factors, messages.

    Propositions: p1=man(socrates), p2=mortal(socrates)
    Groups: g1=(p1)
    Factors: and1(p1→g1), or1(g1→p2)
    Evidence: p1=True
    Query: P(p2=True)

## Data Flow

### Forward (Parsing)

    "Socrates is a man"
        ↓ tokenize
    ["Socrates", "is", "a", "man"]
        ↓ dependency parse
    nsubj(is, Socrates), attr(is, man)
        ↓ verb extraction
    verb: "is", args: {nsubj: Socrates, attr: man}
        ↓ entity recognition
    entity: socrates
        ↓ lexicon lookup
    predicate: man{theme: e}
        ↓ logic generation
    man(theme: socrates)

### Backward (Inference)

    Query: mortal(theme: socrates)
        ↓ graph construction
    Build factor graph from query + KB
        ↓ evidence propagation
    Set known facts as evidence
        ↓ belief propagation
    Run π/λ message passing
        ↓ read result
    P(mortal(socrates)) = 1.0

## Components

### Lever (Inference Engine)

The `lever` package handles:
- Logical language parsing (`logical_lang.py`, `logic.py`)
- Grounding (`horn.py`)
- Factor graph construction (`qbbn.py`)
- Belief propagation (`qbbn_bp.py`)
- Pipeline orchestration (`pipeline.py`)

### Message (Test Framework)

The `message` package handles:
- Coverage test definitions
- Syntax validation (`checker.py`)
- Test execution and reporting

## Design Principles

### 1. Explicit Structure

Every step produces an explicit, inspectable representation. No black boxes.

### 2. Compositionality

Meaning is built from parts. Complex predicates from simple ones, complex rules from atomic predicates.

### 3. Bidirectional Inference

Information flows both forward (causes → effects) and backward (effects → causes).

### 4. Graceful Uncertainty

Unknown propositions have probability 0.5. Partial evidence gives partial belief.

### 5. Separation of Concerns

Parsing, grounding, and inference are separate. Each can be improved independently.