# Typed Slot Grammar

## What It Is

Typed Slot Grammar is a pattern-matching grammar formalism where semantic role signatures serve as syntactic categories. Every grammar rule is a sequence of typed slots that simultaneously matches surface tokens and produces a logical form.

The grammar has no NP, VP, or S. It has no phrase structure. It has no lambda calculus. A word's semantic role signature — {theme: e}, {agent: e, patient: e} — determines where it can appear in a pattern, and the pattern determines the logical form.

## A Rule

A rule has three parts:

    name:     copular_fact
    pattern:  $x:e COP A $P:{theme:e}
    template: $P(theme: $x)

The pattern is a sequence of typed slots:

- `$x:e` — match a token whose lexicon entry has type `e` (entity)
- `COP` — match a copular verb (is, are, was, were)
- `A` — match an article (a, an)
- `$P:{theme:e}` — match a token whose lexicon entry has role signature `{theme: e}`

The template uses the bound variables to produce the output.

## Derivation

Sentence: "Socrates is a man."

Lexicon lookup:
- "Socrates" → socrates : e
- "is" → COP
- "a" → A
- "man" → man : {theme: e}

Pattern match against `$x:e COP A $P:{theme:e}`:
- $x binds to socrates
- COP matches "is"
- A matches "a"
- $P binds to man

Template application: `$P(theme: $x)` → `man(theme: socrates)`

The output is a typed logical form. No parse tree is produced. The pattern match is the parse.

## The Type System

The lexicon assigns every word a role signature:

    predicate man {theme: e}          — unary, one entity argument
    predicate trust {agent: e, patient: e}  — binary, two entity arguments
    predicate king_of {theme: e, reference: e}  — binary, with roles
    entity socrates : e               — an individual

The role signature is the word's type. Grammar rules constrain which types can appear in which positions. A slot expecting `{theme: e}` will match "man", "mortal", "funny", "tall" — any unary predicate — but will not match "trust" or "socrates".

This is the key move: the type system replaces syntactic categories. There is no need to distinguish adjective from noun if both have signature `{theme: e}`. There is no need to distinguish transitive verb from preposition if both have signature `{agent: e, patient: e}`. The grammar dispatches on what a word MEANS, not what part of speech it is.

## Keywords

A small closed class of function words are matched by keyword type rather than lexicon lookup:

    COP     — is, are, was, were, am, be
    ALL     — all, every
    IF      — if, when, whenever
    THEN    — then
    AND     — and
    NOT     — not, never, no, n't
    A       — a, an
    SOMEONE — someone, somebody, anyone

These are the grammatical skeleton. The content words come from the lexicon. The grammar rules weave them together.

## Facts and Rules

The grammar produces two kinds of output:

**Facts** are ground assertions about specific entities:

    man(theme: socrates)
    trust(agent: jack, patient: jill)
    king_of(theme: henry, reference: france)

**Rules** are universally quantified conditionals:

    always [x:e]: man(theme: x) -> mortal(theme: x)
    always [x:e, y:e]: trust(agent: x, patient: y) & trust(agent: y, patient: x) -> allied(agent: x, patient: y)

The output type is determined by the grammar rule, not by the sentence. Copular patterns produce facts. Universal and conditional patterns produce rules.

## Multi-Word Entities

The lexicon supports multi-word forms:

    entity clark : e
      forms: Clark Kent, clark

During matching, the lookup tries longest match first. At token position "Clark", it tries "Clark Kent" (two tokens), finds a match, and consumes both tokens. The rest of the grammar sees a single entity binding.

## Ignored Tokens

A set of function words are automatically skipped during matching:

    the, it, they, he, she, them, to, of, than

These words carry syntactic information in English but do not contribute to the logical form. The grammar steps over them. This allows "The ball is in the box" to match the same pattern as "Ball is in box" — the articles and prepositions are transparent.

## Backtracking

The matcher uses recursive descent with backtracking. When a slot match fails, it tries skipping the current token (if it is in the ignored set) and retrying. When an entire rule fails, the next rule is tried. All matching rules produce outputs — ambiguity is reported when multiple rules match the same sentence.

## Relation to Existing Formalisms

**Definite Clause Grammars.** DCGs are the closest relative. Both build syntax and semantics simultaneously through pattern matching with backtracking. The difference: DCGs dispatch on syntactic categories (s, np, vp) and inherit recursion from Prolog. Typed Slot Grammar dispatches on semantic role types and inherits type checking from the lexicon.

**HPSG.** Head-Driven Phrase Structure Grammar uses typed feature structures that unify during parsing. Our role signatures are a simplified version of typed feature structures — flat rather than nested, with no inheritance hierarchy. HPSG's principle that grammatical information belongs in the lexicon rather than in the rules is directly reflected in our design.

**CCG.** Combinatory Categorial Grammar composes syntax and semantics in parallel through function application. Both formalisms achieve transparent syntax-semantics mapping, but through different mechanisms. CCG uses directional slash categories and combinatory rules. We use flat typed patterns and template instantiation.

**Synchronous Grammars.** Each of our rules is implicitly a synchronous derivation — a source pattern and a target template linked by shared variables. Classical synchronous grammars maintain separate source and target nonterminals. We collapse them because our type system serves both roles.

## Design Principles

**The lexicon does the work.** Most grammatical information lives in lexical entries. The grammar rules are few and general. Adding vocabulary means adding lexicon entries, not grammar rules.

**Semantic types replace syntactic categories.** There is no need for NP, VP, or S when the role signature already encodes argument structure. The grammar dispatches on meaning, not on form.

**Flat rules, transparent output.** Each rule is a single pattern that produces a single logical form. No intermediate representation, no tree, no lambda reduction. What you see in the rule is what you get in the output.

**Extensibility over expressiveness.** Adding a new sentence pattern means adding one rule. The rule is self-contained — it does not interact with other rules. This makes the grammar easy to grow incrementally.