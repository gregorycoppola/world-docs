# Contribution 3: Syntax

## The Problem

How do we get from raw natural language text to the typed logical forms the inference engine requires? The sentence "All men are mortal" must become `always [x:e]: man(theme: x) -> mortal(theme: x)` with the correct predicate names, role labels, quantifier scope, and variable binding. Natural language provides none of this explicitly.

## What We Contribute

A grammar-first, agent-assisted parsing architecture in which a deterministic grammar provides exactness and LLM-assisted disambiguation provides coverage. The architecture is:

    LLM preprocesses → Grammar parses → LLM reranks → QBBN infers

### The Typed Slot Grammar

A grammar of 16 rules where the type signatures in the lexicon fully determine which rule applies to each sentence. The parser:

1. Tokenizes the sentence
2. Looks up content words in the lexicon, binding to typed predicates or entities
3. Skips function words (the, to, of, a, an, etc.)
4. Dispatches to a grammar rule based on the pattern of matched types
5. Emits the logical form

**Fact patterns** (7 rules): copular fact, copular fact bare, negated copular, prepositional copular, copular identity, transitive fact, intransitive fact.

**Rule patterns** (9 rules): copular universal, negated universal, copular generic, reciprocal conditional, and several conditional variants.

### Key Property: Syntactic Completeness

All ambiguity is resolved before the grammar runs. The grammar itself is purely deterministic — given the same lexicon and sentence, it always produces the same logical form. Every sentence produces exactly one parse. Zero ambiguity.

This is not an accident. It is a consequence of type-driven dispatch: the role signatures in the lexicon fully determine which rule applies. If the lexicon says `trust` is `{agent: e, patient: e}` and `man` is `{theme: e}`, then "Jack trusts Jill" can only match the transitive fact pattern and "Socrates is a man" can only match the copular fact pattern.

### LLM-Assisted Preprocessing

The grammar achieves perfect precision on pre-disambiguated input. Real-world text is not pre-disambiguated. The insight is that LLMs can perform the preprocessing that makes grammar-based parsing tractable:

1. Spelling correction and normalization
2. Tokenization and segmentation
3. Lemmatization (reducing to canonical forms matching lexicon entries)
4. Word sense disambiguation (selecting the correct lexicon entry)
5. Part-of-speech tagging (constraining syntactic category)

Evidence:

- POS tagging: 89.6–91.1% accuracy (residual errors largely annotation conventions)
- PP attachment: 95% accuracy vs. 50% for the Stanford parser (p = 2.0 × 10⁻⁵)
- Parse reranking: 95% binary accuracy when directed to evaluate a specific construction
- Direct LLM parsing: 12.4% UAS — LLMs cannot replace grammars for structured output

The negative result is as important as the positive results. LLMs understand structure but cannot produce exact formal representations. Grammars produce exact representations but break on ambiguity. Together they cover open-domain text.

### The Scaling Argument

Coverage grows by adding new patterns — grammar rules, lexicon entries, reasoning categories — not by collecting more data per pattern. Each new grammar rule handles a class of sentences (e.g., "copular universal" handles all sentences of the form "All X are Y"). The LLM preprocessing handles variation within each class.

This is qualitatively different from neural semantic parsing, where coverage grows by training on more examples of the same patterns.

### What Is Not Yet Demonstrated

Full end-to-end parsing of open-domain text. The current evaluation uses controlled test suites with pre-specified lexicons. The argument that LLM preprocessing scales to open-domain text is supported by evidence but not yet demonstrated as a running system. The end-to-end pipeline — LLM preprocesses, grammar parses, LLM reranks, QBBN infers — is designed but not yet wired together for arbitrary input.

## Evidence

- 33/33 sentences parsed across 12 test suites
- 33/33 gold facts derived
- Zero ambiguous parses
- 12 of 16 grammar rules exercised
- Sentence length: 3 tokens ("Superman flies.") to 14 tokens ("If two people trust each other, they are allies.")