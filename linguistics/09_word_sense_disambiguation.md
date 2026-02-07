# Word Sense Disambiguation

## Overview

Word Sense Disambiguation (WSD) is the task of determining which sense of a word is used in a given context.

Example:

    "I deposited money in the bank."
    bank = financial institution (not river bank)
    
    "I sat on the bank of the river."
    bank = river bank (not financial institution)

## Why It Matters

Many words are ambiguous:

| Word | Senses |
|------|--------|
| bank | financial, river, verb (rely on) |
| run | move fast, operate, stream, series |
| set | place, collection, ready, harden |
| light | illumination, not heavy, pale |

"Set" has 430 senses in WordNet — the most of any English word.

## Sense Inventories

### WordNet

The standard sense inventory for English:

    bank.n.01: financial institution
    bank.n.02: sloping land beside water
    bank.n.03: supply or stock held in reserve
    bank.v.01: tip laterally (airplane)
    bank.v.05: deposit in a bank

Organized into synsets (synonym sets) with relations:
- Hypernyms: bank.n.01 → financial_institution → institution
- Hyponyms: bank.n.01 → commercial_bank, savings_bank
- Meronyms: bank.n.01 → vault, teller

### FrameNet

Senses defined by semantic frames:

    bank (financial) → frame: Businesses
    bank (river) → frame: Natural_features

### OntoNotes

Coarser sense distinctions, higher agreement.

## Approaches

### Knowledge-Based

**Lesk Algorithm**: Choose sense whose definition has most word overlap with context.

    Context: "I deposited money in the bank."
    
    bank.n.01 definition: "a financial institution..."
    bank.n.02 definition: "sloping land beside water..."
    
    "money", "deposited" → more similar to bank.n.01

**Graph-Based**: Use WordNet graph structure, find most connected sense.

### Supervised

Train classifier on sense-annotated data:

- Features: surrounding words, POS, syntactic relations
- Models: SVM, neural networks

Challenge: Need annotated data for every word.

### Neural

**Context Embeddings**: BERT gives different embeddings for same word in different contexts.

    embed("bank" in "river bank") ≠ embed("bank" in "money in bank")

Compare to sense embeddings, choose closest.

State-of-the-art: ~80% F1 on standard benchmarks.

## Relevance to QBBN

WSD determines which predicate a word maps to:

    "John went to the bank"
    
    If bank = financial:
      go(agent: john, goal: bank_financial_1)
    
    If bank = river:
      go(agent: john, goal: bank_river_1)

### Predicate Selection

Our lexicon may have multiple entries:

    bank_financial: {theme: e}
    bank_river: {location: loc}

WSD selects the right one.

### Argument Compatibility

Senses have different argument structures:

    run (move fast): run{agent: e, path: loc}
    run (operate): run{agent: e, patient: machine}
    run (stream): run{theme: liquid, path: loc}

The correct sense determines valid arguments.

## Challenges

### Fine-Grained Distinctions

    "The plane banked left."
    "I'm banking on you."
    
    Both are verb senses of "bank" but very different.

### Domain Dependence

    "Python" in a programming context vs. biology context.

### Rare Senses

Common senses dominate training data; rare senses hard to learn.

## Practical Approach

For QBBN, we take a pragmatic approach:

1. **Context-based heuristics**: Use surrounding words
2. **Domain restriction**: Limit to relevant senses
3. **Fallback**: If ambiguous, create ambiguous logical form

    "John went to the bank"
    
    If context unclear:
      go(agent: john, goal: bank_?)
      
    Flag for resolution by downstream reasoning or user query.

## Tools

**NLTK + WordNet**: Basic Lesk implementation

**BERT-WSD**: Neural sense disambiguation

**UKB**: Graph-based WSD using WordNet