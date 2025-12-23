## Constrained Decoding (Runtime Grammar Enforcement)

These take a standard transformer and mask invalid tokens at decode time:

**PICARD** (Scholak et al., EMNLP 2021)
- Constrains auto-regressive decoders through incremental parsing, rejecting inadmissible tokens at each decoding step. Uses attoparsec for incremental SQL parsing. No retraining needed - works with any seq2seq model.
- https://arxiv.org/abs/2109.05093

**Synchromesh** (Poesia et al., ICLR 2022)
- Constrained Semantic Decoding (CSD) enforces rich syntactic and semantic constraints during code generation - including scoping and type-checking - on a frozen language model.
- Tested on SQL, Vega-Lite, SMCalFlow
- https://arxiv.org/abs/2201.11227

**SynCode** (Ugare et al., 2024)
- Ensures soundness and completeness with respect to the CFG of a formal language using an offline-constructed DFA mask store derived from the grammar.
- Reduces 96.07% of syntax errors in generated Python and Go code.
- Very fast - claims ~10% overhead
- https://github.com/uiuc-focal-lab/syncode

**GCD (Grammar-Constrained Decoding)** (EPFL, 2023)
- Integrates seamlessly with the Transformers library using EBNF grammar interface.
- https://github.com/epfl-dlab/GCD

**Recent: Context-Sensitive Constrained Decoding** (August 2025)
- A tree of parsers (ToP) approach that handles semantic correctness beyond just CFG - including variable scopes and type constraints. This goes beyond syntax to runtime correctness.

---

## Architectural Approaches (Decoder Structure)

These build the grammar into the model architecture itself:

**Abstract Syntax Networks** (Rabinovich, Stern, Klein - ACL 2017)
- A modular decoder whose submodels are composed to natively generate ASTs in a top-down manner. The decoding process follows a dynamically chosen mutual recursion between modules, where the structure of the tree being produced mirrors the call graph.
- Each module corresponds to a grammar construct
- https://arxiv.org/abs/1704.07535

**TranX** (Yin & Neubig, 2017-2018)
- Transition-based neural abstract syntax parser
- Outputs action sequences that construct ASTs by construction

**StructCoder** (2022)
- Structure-aware Transformer decoder trained to predict AST paths and data flow edges alongside token generation.
- https://arxiv.org/abs/2206.05239

---

## The Key Distinction You're After

The constrained decoding papers do exactly what you described - they literally make it *impossible* for the model to output invalid syntax by masking logits at each step. The model's distribution is projected onto the valid continuation set defined by the grammar.

The architectural approaches (ASNs, TranX) go further - they don't even have a "token" output space in the traditional sense. They output *grammar production rules* or *AST construction actions*.

---

## Practical Libraries

- **llama.cpp** has built-in GBNF grammar support
- **Outlines** - Python library for structured generation
- **Guidance** - Microsoft's library with CFG support
- **XGrammar** - high-performance grammar engine

There's also a curated list at https://github.com/Saibo-creator/Awesome-LLM-Constrained-Decoding that tracks this whole space.

---

The tradeoff you'll see discussed: constrained decoding can hurt "reasoning" - there's recent work like **CRANE** showing that constraining outputs to very restrictive grammars that only allow syntactically valid final answers can reduce reasoning capabilities, so they alternate between unconstrained reasoning and constrained output phases.
