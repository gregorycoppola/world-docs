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