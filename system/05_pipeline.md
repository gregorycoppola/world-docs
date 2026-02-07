# End-to-End Pipeline

## Overview

The complete pipeline transforms English text into probabilistic answers:

    English → Syntax → Semantics → Logic → Grounding → Inference → Answer

Each stage is modular and can be improved independently.

## Stage 1: Syntax Processing

### Input
Raw English text.

### Layers

**Tokenization**: Split into sentences and words.

**Dependency Parsing**: Identify grammatical relations.

    "John loves Mary"
    → nsubj(loves, John), dobj(loves, Mary)

**Verb Extraction**: Identify predicates and their frames.

    verb: loves
    frame: {nsubj: John, dobj: Mary}

**Clause Segmentation**: Handle complex sentences.

    "John loves Mary and Mary loves John"
    → clause1: "John loves Mary"
       clause2: "Mary loves John"

### Output
Structured syntactic representation with verbs, arguments, and clause boundaries.

## Stage 2: Semantic Processing

### Layers

**Entity Recognition**: Identify and type entities.

    "John" → entity(john, person)
    "Paris" → entity(paris, city)

**Coreference Resolution**: Link mentions to entities.

    "John went home. He ate dinner."
    → "He" = john

**Lexicon Lookup**: Map words to predicates.

    "loves" → predicate love{agent: e, patient: e}
    "tall" → predicate tall{theme: e}

**Argument Mapping**: Map syntactic roles to semantic roles.

    nsubj → agent
    dobj → patient
    iobj → recipient

### Output
Semantic frames with resolved entities and mapped roles.

## Stage 3: Logic Generation

### Process

**Fact Generation**: Ground predicates from assertions.

    "Socrates is a man"
    → man(theme: socrates)

**Rule Extraction**: Identify implications with modality.

    "All men are mortal"
    → always [x:e]: man(theme: x) -> mortal(theme: x)
    
    "Dogs usually bark"
    → usually [x:e]: dog(theme: x) -> bark(agent: x)

**Query Formation**: Parse questions.

    "Is Socrates mortal?"
    → ? mortal(theme: socrates)

### Output
A LogicalDocument with predicates, entities, facts, rules, and query.

## Stage 4: Grounding

### Process

**Entity Inventory**: Collect all relevant entities.

**Rule Instantiation**: Ground rules for specific entities.

    Rule: always [x:e]: man(theme: x) -> mortal(theme: x)
    Entity: socrates
    → man(theme: socrates) -> mortal(theme: socrates)

**Clause Generation**: Create Horn clauses with premises, conclusion, and weight.

### Output
A set of grounded HornClause objects.

## Stage 5: Factor Graph Construction

### Process

**Query-Driven Expansion**: Start from query, expand forward and backward.

**Node Creation**: Create proposition and group nodes.

**Factor Creation**: Create AND, OR, and NEG factors.

**Evidence Setting**: Mark known facts.

### Output
A QBBNGraph with variables and factors.

## Stage 6: Inference

### Process

**Initialization**: Set π and λ for all variables.

**Iteration**: Run forward and backward passes until convergence.

**Belief Extraction**: Compute P(query).

### Output
Probability of query + trace of reasoning.

## CLI Interface

Run full pipeline:

    lever pipeline run "Socrates is a man. All men are mortal." --query "Is Socrates mortal?"

Verify against expected answer:

    lever pipeline verify tests/socrates/

Run all coverage tests:

    lever pipeline verify-all coverage/

## Test File Format

Each test case has multiple files:

    test_name/
    ├── test_name.document   # English input
    ├── test_name.lexicon    # Predicate declarations
    ├── test_name.facts      # Logical facts and rules
    ├── test_name.query      # The query
    ├── test_name.expected   # Expected answer (yes/no/unknown)
    ├── test_name.question   # Natural language question
    └── test_name.description # What this tests

## Error Handling

Each stage can fail:
- **Syntax**: Unparseable input
- **Semantics**: Unknown words, failed coreference
- **Logic**: Invalid logical form
- **Grounding**: Missing entities
- **Inference**: Non-convergence

Errors are reported with stage and context for debugging.