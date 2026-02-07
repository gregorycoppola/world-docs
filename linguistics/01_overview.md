# Linguistics Overview

## The Foundation

Our logical language draws on three major traditions in linguistics and formal semantics:

1. **Chomsky** — Syntax is generative, hierarchical structure
2. **Montague** — Semantics is compositional, meaning as logic
3. **Steedman** — CCG connects syntax to semantics transparently

## The Key Insight

Natural language has two "interfaces":

- **PF (Phonetic Form)** — What you hear/read
- **LF (Logical Form)** — What it means

Our system operationalizes this: we parse PF to LF, then reason over LF.

## What We Take From Each Tradition

From **Chomsky**: The idea that language has hierarchical structure governed by rules. Sentences aren't just word sequences — they have constituency and dependencies.

From **Montague**: The principle of compositionality — the meaning of a complex expression is determined by the meanings of its parts and how they combine. And the radical claim that natural language can be given the same rigorous treatment as formal logic.

From **Steedman**: The practical methods for parsing to logical form. Combinatory Categorial Grammar (CCG) provides a transparent syntax-semantics interface where every syntactic operation has a semantic counterpart.

## Our Pipeline

The NLP pipeline implements these ideas:

    Text
     │
     ├─→ Tokenization (words)
     ├─→ Dependency Parsing (structure)
     ├─→ Semantic Role Labeling (who did what to whom)
     ├─→ Coreference Resolution (linking mentions)
     ├─→ Word Sense Disambiguation (which meaning)
     │
     ▼
    Logical Form

Each stage is informed by linguistic theory but implemented with modern NLP tools.

## Document Structure

This section covers:

- `02_chomsky.md` — Generative grammar, the syntax of structure
- `03_montague.md` — Formal semantics, meaning as logic
- `04_steedman.md` — CCG, transparent syntax-semantics
- `05_nlp_pipeline.md` — The practical parsing pipeline
- `06_dependency_parsing.md` — Grammatical relations
- `07_semantic_role_labeling.md` — Predicate-argument structure
- `08_coreference.md` — Linking mentions to entities
- `09_word_sense_disambiguation.md` — Resolving lexical ambiguity