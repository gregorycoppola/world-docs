# world-docs

Documentation for the **world** modeling pipeline — a system for translating natural language into logical form and performing probabilistic inference.

**By Super Sonic Vibes ✈️🪩✨**

## Overview

This repository contains the theoretical foundations and technical documentation for the Quantified Boolean Bayesian Network (QBBN), a system that addresses five fundamental problems with Large Language Models:

1. **Hallucinations** — QBBN only asserts what it can trace back to evidence
2. **Reasoning** — Proper logical inference via belief propagation
3. **Planning** — Backward inference over temporal propositions
4. **Continuous Learning** — Incremental KB updates without retraining
5. **World Models** — Explicit, inspectable knowledge representation

## Contents

- **[applications/](applications/)** — How QBBN addresses the five LLM problems
- **[system/](system/)** — Architecture, logical language, factor graphs, pipeline
- **[inference/](inference/)** — AND/OR/NEG factors, quantification, message passing, convergence
- **[models/](models/)** — Background on Bayesian networks, belief propagation, neural networks, transformers
- **[linguistics/](linguistics/)** — Chomsky, Montague, Steedman, NLP pipeline, parsing, SRL, coreference
- **[related_work/](related_work/)** — Comparison to MLN, ProbLog, neural theorem provers, neuro-symbolic AI
- **[experiments/](experiments/)** — Coverage tests, results, case studies
- **[future/](future/)** — Learning, scaling, LLM integration

## Key Ideas

**The Two Interfaces**: Natural language has two representations — surface form (text) and logical form (meaning). We parse from one to the other.

**Factor Graph Structure**: Propositions connect via AND factors (conjunction) to groups, which connect via OR factors (noisy-OR) to conclusions. This bipartite structure enables efficient belief propagation.

**Bidirectional Inference**: π messages flow forward (causes → effects), λ messages flow backward (effects → causes). This enables both prediction and contrapositive reasoning.

## Implementation

See [gregorycoppola/world](https://github.com/gregorycoppola/world) for the working code.

Current status: **40/44 coverage tests passing (91%)**

## License

Apache 2.0