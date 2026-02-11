# Three Contributions: Syntax, Semantics, Inference

## The Classical Division

Natural language processing has three classical areas, corresponding to three questions:

1. **Syntax** — How do we get from raw text to structure?
2. **Semantics** — What is the structure we are targeting?
3. **Inference** — What can we do with that structure once we have it?

These are not our invention. They trace back through the entire history of the field — from Chomsky (syntax, 1957), through Montague (semantics, 1970), through the AI reasoning tradition from McCarthy and Hayes (inference, 1969) onward. What we contribute is a specific answer to each question, and a demonstration that the three answers compose into a working system.

## Contribution 1: Inference — The QBBN

**Question**: Given a knowledge base of logical facts and rules, how do we answer queries?

**Our answer**: The Quantified Boolean Bayesian Network (Coppola, 2024). A factor graph with three factor types — AND (Ψ_and), OR (Ψ_or), and NEG (Ψ_neg) — over which Pearl-style belief propagation computes posterior probabilities.

**What is novel**:

The boolean decomposition. All logical structure is reduced to AND, OR, and NEG factors. This is not how standard Bayesian networks or Markov Logic Networks work. Standard approaches either use conditional probability tables (Bayesian networks) or weighted formulas over undirected graphs (MLNs). Our decomposition maps directly onto the simple elimination rules of Prawitz's natural deduction:

- →-Elimination (modus ponens) = forward pass through AND → OR
- ∧-Introduction / ∧-Elimination = AND factors
- ∨-Introduction = OR factors (noisy-OR combining multiple derivations)
- Contrapositive reasoning = NEG factor + backward λ messages

The NEG factor is the specific innovation that enables modus tollens within belief propagation. Standard belief propagation on Bayesian networks does not naturally handle contrapositive reasoning. By adding a factor that enforces P(X) + P(¬X) = 1, negative evidence propagates backward through rules, enabling "Zeus is not mortal, all men are mortal, therefore Zeus is not a man."

The system implements what Prawitz (1965) calls the forward fragment — only the simple elimination rules, no case analysis, no hypothetical reasoning. This is why it is tractable: the forward fragment is linear time, and belief propagation converges in 2–3 iterations on all 44 test cases.

**What is not claimed**: We do not claim full first-order theorem proving. The system handles universally quantified Horn clauses with negation. It does not handle existential quantification in rule premises, disjunction in premises, or nested quantifier scope. These belong to the query and planning fragments of natural deduction, which are harder (NP-hard and undecidable respectively).

## Contribution 2: Semantics — The Typed Logical Language

**Question**: What formal language do we translate natural language into?

**Our answer**: A typed predicate language with semantic roles, quantification, negation, and modal weights, designed to be close to surface structure while remaining formally precise.

**What is novel**:

The combination of three properties that no existing system offers together:

1. **Role-labeled arguments** rather than positional. `trust(agent: jack, patient: jill)` not `trust(jack, jill)`. This bridges the gap between dependency parsing output (which produces labeled grammatical relations) and logical form (which traditional systems express with positional arguments). The labels are semantic roles from the linguistics tradition (Fillmore, 1968; Dowty, 1991).

2. **Modal quantifiers** that map natural language hedging to probabilistic weights. "Always" → 0.99, "usually" → 0.9, "sometimes" → 0.5. These are not ad hoc — they parameterize the noisy-OR factors in the inference engine, giving a principled bridge between the vagueness of natural language and the precision of probabilistic inference.

3. **A type system grounded in Prawitz's proof theory**. The current system handles first-order types (entities and predicates). The design extends to second-order types (predicate variables, lambda abstraction — Prawitz Chapter V) and modal types (necessity, possibility — Prawitz Chapter VI). This extension path is not speculative — Prawitz provides the introduction/elimination rules, and our fragment hierarchy tells us which extensions remain tractable (lambda composition/decomposition = forward fragment, existential predicate search = query fragment).

**The sufficiency claim**: We assert that this language, fully extended through second-order and modal types following Prawitz, is sufficient to represent all human language semantics. This is a strong claim. The justification is that Prawitz's system covers first-order logic, second-order logic (predicate quantification and lambda), and modal logic (necessity, possibility, and by extension propositional attitudes like belief and desire). Montague (1970, 1973) demonstrated that natural language semantics can be captured in an intensional type theory — our language is a role-labeled, probabilistically weighted variant of the same mathematical objects.

**What is not yet implemented**: The current paper and code implement the first-order fragment. Second-order features (lambda classes, predicate variables) and modal features (intensional verbs, propositional attitudes) are designed but not yet in the grammar or inference engine. The world-docs trace the full design trajectory.

## Contribution 3: Syntax — Grammar-First, Agent-Assisted Parsing

**Question**: How do we get from raw text to the logical forms the inference engine requires?

**Our answer**: A typed slot grammar that deterministically compiles disambiguated input to logical form, combined with LLM-assisted preprocessing that resolves ambiguity before the grammar runs.

**What is novel**:

The architecture, not the individual components. Grammars are old. LLMs are new. The insight is that they are complementary in a precise way:

- **Grammars** handle structure — given unambiguous input, they produce exact, guaranteed-correct logical forms. But they break on ambiguity.
- **LLMs** handle ambiguity — they can resolve word sense, PP attachment, and syntactic category with near-perfect accuracy. But they cannot produce exact formal representations (12.4% UAS on zero-shot structured parsing).

The pipeline is: LLM preprocesses → Grammar parses → LLM reranks → QBBN infers.

The key property is **syntactic completeness**: all ambiguity is resolved before the grammar runs, so the grammar itself is purely deterministic. Every sentence produces exactly one parse. This is not an accident — it is a consequence of type-driven dispatch, where the role signatures in the lexicon fully determine which grammar rule applies.

**The scaling argument**: Coverage grows by adding new patterns — grammar rules, lexicon entries, reasoning categories — not by collecting more data per pattern. Each new grammar rule handles a class of sentences (e.g., "copular universal" handles all sentences of the form "All X are Y"). The LLM preprocessing handles the variation within each class. This is qualitatively different from neural semantic parsing, where coverage grows by training on more examples.

**What is not yet demonstrated**: Full end-to-end parsing of open-domain text. The current evaluation uses controlled test suites with pre-specified lexicons. The argument that LLM preprocessing scales this to open-domain text is supported by evidence (95% PP attachment accuracy, 95% parse critique accuracy) but not yet demonstrated as a running system.

## How the Three Compose

The three contributions form a pipeline:

    Text → [Syntax] → Logical Form → [Semantics defines the target] → Factor Graph → [Inference] → Answer

Each can be evaluated independently:
- Syntax: does the grammar produce the correct logical forms? (33/33)
- Inference: does the engine produce the correct answers? (44/44)
- Semantics: is the language expressive enough? (covers 22 reasoning patterns, with a design path to full coverage)

And they compose: for the 12 reasoning categories covered by both the grammar and the inference engine, we have independent verification that text maps to correct logical forms and that those logical forms produce correct answers. Wiring them into a single end-to-end pipeline is straightforward engineering.

## The Big Picture

The vision is a system that can read natural language documents, convert them to precise logical representations, and reason over them without hallucination — because every conclusion traces through explicit rules and typed propositions in the factor graph.

This is an old vision. It was the goal of the knowledge representation community in the 1970s and 1980s, and it failed because symbolic systems couldn't handle the ambiguity and variation of natural language. What has changed is that LLMs can now handle ambiguity — not as reasoners, but as preprocessors. The grammar handles structure. The QBBN handles inference. The LLM handles the messy surface of language. Each does what it is good at.