# Future Work: LLM Integration

## The Vision

A complete system where:

1. User asks a question in English
2. LLM parses to logical form
3. QBBN performs inference
4. LLM generates natural language answer

Each component does what it's best at:
- LLM: Language understanding and generation
- QBBN: Reliable reasoning

## Current State

### What Works

Manual pipeline:
- Hand-written logical forms
- QBBN inference
- 91% accuracy on coverage tests

### What's Missing

Automatic parsing:
- English → logical form
- Entity recognition and linking
- Rule extraction from text

## Parsing Pipeline

### Architecture

    English Text
         │
         ▼
    ┌─────────────┐
    │   Syntax    │  spaCy / dependency parse
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │  Entities   │  NER + linking
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │   Frames    │  SRL / verb frames
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │   Logic     │  LLM-assisted mapping
    └─────────────┘
         │
         ▼
    Logical Form

### LLM Role in Parsing

The LLM can help with:

**Lexicon mapping**: What predicate does "loves" map to?

    Input: "John loves Mary"
    LLM: love(agent: john, patient: mary)

**Rule extraction**: What rules does this sentence express?

    Input: "All men are mortal"
    LLM: always [x:e]: man(theme: x) -> mortal(theme: x)

**Coreference**: What does "he" refer to?

    Input: "John went home. He ate dinner."
    LLM: he = john

**Disambiguation**: Which sense of "bank"?

    Input: "I went to the bank"
    Context: financial discussion
    LLM: bank_financial, not bank_river

### Challenges

**Consistency**: LLM output must match our logical language exactly.

**Coverage**: Handle all constructions in English.

**Errors**: LLM will make mistakes — need verification/correction.

## Generation Pipeline

### Architecture

    Inference Result
         │
         ▼
    ┌─────────────┐
    │  Explain    │  Extract reasoning chain
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │  Template   │  Structure the explanation
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │    LLM      │  Generate fluent text
    └─────────────┘
         │
         ▼
    Natural Language Answer

### Example

Query: "Is Socrates mortal?"

Inference result:

    P(mortal(socrates)) = 1.0
    Reasoning chain:
      1. man(socrates) = True [evidence]
      2. man(x) -> mortal(x) [rule, weight=99]
      3. mortal(socrates) = True [derived]

Generated answer:

    "Yes, Socrates is mortal. This follows from the fact that 
     Socrates is a man, combined with the rule that all men 
     are mortal."

### Challenges

**Faithfulness**: Generated text must accurately reflect the reasoning.

**Fluency**: Output should be natural, not robotic.

**Calibration**: Express uncertainty appropriately.

## Retrieval-Augmented Reasoning

Combine QBBN with retrieval for open-domain QA:

### Architecture

    Question
         │
         ▼
    ┌─────────────┐
    │  Retrieve   │  Find relevant documents
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │   Parse     │  Extract facts and rules
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │    QBBN     │  Reason over extracted KB
    └─────────────┘
         │
         ▼
    Answer + Explanation

### Benefits Over Pure RAG

**Consistency**: QBBN ensures P(X) + P(¬X) = 1.

**Multi-hop**: Chain reasoning across documents.

**Explanation**: Trace exactly which facts support the answer.

**Contradiction detection**: Identify conflicting sources.

## Training Paradigm

### Supervised Parsing

Train LLM to produce logical forms:

    Input: "John loves Mary"
    Target: love(agent: john, patient: mary)

Need: Parallel corpus of English + logical forms.

### Reinforcement from Inference

Use QBBN inference as reward signal:

    1. LLM proposes logical form
    2. QBBN checks consistency
    3. Reward LLM for consistent parses

### Self-Consistency

Generate multiple parses, keep consistent ones:

    Parse 1: love(agent: john, patient: mary)
    Parse 2: love(agent: mary, patient: john)  # inconsistent
    Parse 3: love(agent: john, patient: mary)
    
    Majority: love(agent: john, patient: mary)

## Practical Path Forward

### Phase 1: Structured Prompting

Prompt LLM with logical language specification:

    "Convert to logical form using predicates:
     love{agent: e, patient: e}
     man{theme: e}
     ..."

### Phase 2: Fine-Tuned Parser

Fine-tune smaller model on parsing task:
- Faster inference
- More consistent output
- Domain-specific

### Phase 3: End-to-End Training

Joint training of parser + reasoner:
- Differentiable or RL-based
- Optimize for final answer accuracy
- Learn what logical forms are useful

### Phase 4: Production System

Full pipeline with:
- Error handling
- Fallback to LLM-only for unparseable queries
- Confidence calibration
- Continuous improvement from user feedback