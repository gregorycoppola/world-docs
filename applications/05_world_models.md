# World Models

## The Problem

LLMs have no explicit representation of what they know. Their knowledge is distributed across billions of parameters, learned during training, and inaccessible to inspection.

This creates four concrete failures:

- **No inventory of beliefs**: You cannot ask an LLM "what do you know about Socrates?" and get a complete, bounded answer. It will generate text that may or may not reflect what's encoded in its weights.
- **No consistency checking**: An LLM can assert "Paris is the capital of France" in one context and behave as if Lyon is the capital in another. There is no mechanism to detect or prevent this.
- **No explanation**: When an LLM gives an answer, there is no way to ask "why?" and receive a grounded explanation. Chain-of-thought prompting generates plausible reasoning text, but the text is generated the same way as the answer — by token prediction, not by tracing a derivation.
- **No boundaries**: An LLM cannot distinguish between what it knows and what it doesn't. It has no representation of the boundary of its knowledge, so it cannot reliably say "I don't have information about that."

### Why LLMs Lack World Models

The training objective — maximize P(next token | context) — creates a system optimized for generation, not representation. Knowledge is a side effect of learning to predict, not an explicit design goal. The result is a system that behaves as if it knows things, without maintaining any structure that could be called "knowledge" in the traditional sense.

### The Danger

Without an explicit world model:

1. There is no way to audit what the system believes
2. There is no way to verify that beliefs are consistent
3. There is no way to trace conclusions to their sources
4. There is no way to determine the boundaries of the system's knowledge
5. There is no way to correct a specific belief without risking others

## The LBN Solution

In the Logical Bayesian Network, the world model is the knowledge base and factor graph — explicit, typed, inspectable data structures.

### The Knowledge Base

The KB contains exactly what the system knows, in two forms:

**Facts** — ground propositions with truth values:

    man(theme: socrates)
    not mortal(theme: zeus)
    king_of(theme: henry, reference: france)

**Rules** — weighted implications over typed variables:

    always [x:e]: man(theme: x) -> mortal(theme: x)
    usually [x:e]: bird(theme: x) -> flies(theme: x)
    sometimes [x:e, y:e]: meets(agent: x, patient: y) -> likes(agent: x, patient: y)

Every predicate is declared with typed roles. Every entity is declared with a type. There are no hidden beliefs — the KB is the complete inventory of what the system knows.

### The Factor Graph

At inference time, the KB compiles to a factor graph — a bipartite graph of proposition nodes and group nodes connected by AND, OR, and NEG factors. This graph is the system's world model during reasoning:

- **Proposition nodes** represent beliefs with probabilities
- **Group nodes** represent conjunctions of premises
- **AND factors** enforce conjunction (all premises must be true)
- **OR factors** combine evidence from multiple rules via noisy-OR
- **NEG factors** enforce complementarity (P(x) + P(¬x) = 1)

The factor graph is fully inspectable. For any proposition, you can:

1. **Query its probability**: What does the system believe?
2. **Trace its support**: Which rules and facts led to this belief?
3. **Identify its dependencies**: What other beliefs does it depend on?
4. **Check consistency**: Are there contradictions in the supporting evidence?

### Bounded Knowledge

The system knows exactly what's in the KB, and nothing else. This is a feature:

    Query: mortal(socrates)?
    
    If man(socrates) in KB and man(x)->mortal(x) in KB:
      Answer: Yes (P = 1.0)
      Explanation: Derived from man(socrates) via rule
    
    If no relevant facts or rules:
      Answer: Unknown (P = 0.5)
      Explanation: No information about mortal(socrates) in KB

The system cannot fabricate knowledge. If the KB doesn't contain relevant information, the system reports uncertainty — it doesn't hallucinate an answer.

### The Lexicon as Ontology

The lexicon — which declares predicates, their roles, entities, and their types — serves as the system's ontology. It defines:

- What kinds of things exist (entity types)
- What can be said about them (predicates with typed roles)
- What vocabulary maps to what concepts (surface forms to logical forms)

This is an explicit, editable specification of the system's conceptual framework. Extending what the system can represent means adding lexicon entries — not retraining.

## Comparison

| Aspect | LLM | LBN |
|--------|-----|-----|
| Knowledge location | Distributed in weights | Explicit in KB |
| Inventory | Unknown | Complete and enumerable |
| Consistency | Unchecked | Verifiable |
| Explanation | Generated text | Traced derivation |
| Boundaries | Unknown | Defined by KB contents |
| Modification | Retrain | Edit KB |
| Ontology | Implicit | Explicit lexicon |

## Example: Medical Knowledge

    KB:
      symptom(patient: p1, condition: fever)
      symptom(patient: p1, condition: cough)
      always [x:e]: symptom(x, fever) & symptom(x, cough) -> possible(x, flu)
      usually [x:e]: possible(x, flu) & season(winter) -> likely(x, flu)
      season(winter)
    
    Query: likely(p1, flu)?
    Answer: Yes (P ≈ 0.9)
    
    World model inspection:
    - Facts about p1: fever, cough (2 facts)
    - Applicable rules: symptom->possible, possible+season->likely (2 rules)
    - Derivation: fever+cough → possible(flu) → likely(flu) given winter
    - Missing: No test results, no travel history
    
    The system knows what it knows AND what it doesn't know.

## Relationship to Other Problems

World models are foundational — the other four capabilities depend on them:

- **Hallucination prevention** requires knowing what's in the KB (world model) to avoid asserting what isn't
- **Reasoning** requires an explicit structure to reason over (the factor graph)
- **Planning** requires inspecting preconditions and goals (backward traversal of the graph)
- **Continuous learning** requires a modifiable representation (the KB)

The explicit world model is not just one of five capabilities — it is the substrate that enables the other four.

## Limitations

1. **Expressiveness**: The KB can only represent what the logical language can express
2. **Completeness**: The KB only contains what has been explicitly added
3. **Scale**: Large KBs require efficient indexing and grounding algorithms
4. **Acquisition**: Populating the KB still requires parsing natural language — the subject of the current paper

## Key Insight

A world model is not a statistical summary of training data. It is an explicit, inspectable, modifiable representation of what the system knows, how it knows it, and what it doesn't know. The LBN's factor graph provides exactly this.