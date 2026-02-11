# Contributions Overview

## The Three Classical Areas

Natural language processing has three classical areas, corresponding to three questions:

1. **Inference** — What can we do with structured representations once we have them?
2. **Semantics** — What is the structured representation we are targeting?
3. **Syntax** — How do we get from raw text to that representation?

These trace back through the entire history of the field — from the AI reasoning tradition (McCarthy and Hayes, 1969) through formal semantics (Montague, 1970) through generative grammar (Chomsky, 1957). What this project contributes is a specific answer to each question, and a demonstration that the three answers compose into a working system.

## What Is New Overall

The central innovation of the project is the logical Bayesian network — a factor graph that implements logical inference via belief propagation. Probabilistic logic existed before (ProbLog, Markov Logic Networks), but prior systems used either weighted model counting (exact but expensive) or MCMC sampling (slow and doesn't scale). Nobody took the step of decomposing logical rules into AND/OR/NEG factors on a bipartite graph and running Pearl-style belief propagation to get both forward inference (modus ponens) and backward inference (modus tollens) from the same message-passing algorithm.

The 2024 paper (Coppola, "The Quantified Boolean Bayesian Network," arXiv:2402.06557) introduced the AND/OR decomposition and demonstrated convergence on synthetic data. This paper extends the inference engine with NEG factors for contrapositive reasoning and bidirectional graph construction, presents a typed logical language grounded in Prawitz's natural deduction with a sufficiency argument for natural language, and introduces a grammar-first agent-assisted parsing architecture.

## How the Three Compose

The three contributions form a pipeline:

    Text → [Syntax] → Logical Form → [Semantics defines the target] → Factor Graph → [Inference] → Answer

Each can be evaluated independently:

- Inference: does the engine produce correct answers from logical forms? (44/44 across 22 reasoning patterns)
- Semantics: is the language expressive enough? (three tiers of expressiveness following Prawitz, with test coverage at each tier)
- Syntax: does the grammar produce correct logical forms from text? (33/33 sentences, zero ambiguity)

And they compose: for the 12 reasoning categories covered by both the grammar and the inference engine, we have independent verification that text maps to correct logical forms and that those logical forms produce correct answers.

## The Vision

A system that reads natural language documents, converts them to precise logical representations, and reasons over them without hallucination — because every conclusion traces through explicit rules and typed propositions in the factor graph.

This was the goal of the knowledge representation community in the 1970s and 1980s, and it failed because symbolic systems couldn't handle the ambiguity and variation of natural language. What has changed is that LLMs can now handle ambiguity — not as reasoners, but as preprocessors. The grammar handles structure. The QBBN handles inference. The LLM handles the messy surface of language. Each does what it is good at.