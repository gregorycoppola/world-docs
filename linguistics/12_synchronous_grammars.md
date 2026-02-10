# Synchronous Grammars

## Origins

Synchronous grammars were developed in the 1990s and 2000s primarily for machine translation. The idea: two grammars derive in parallel, one for each language, linked by shared derivation steps. But the formalism applies equally well to syntax-semantics pairs — one grammar for surface form, one for logical form, deriving together.

## The Core Idea

A synchronous grammar consists of paired rules. Each rule has a source side and a target side, with shared variables linking them:

    S → NP VP,  love(x, y)
    NP → "John",  john
    NP → "Mary",  mary
    VP → "loves" NP,  love(_, y)

When the source side derives "John loves Mary", the target side simultaneously derives `love(john, mary)`. The derivation tree is shared — every expansion on the source side triggers a corresponding expansion on the target side.

## Synchronous CFG

The simplest form is Synchronous Context-Free Grammar (SCFG). Each production has two right-hand sides:

    S → <NP1 VP2, VP2(NP1)>
    VP → <"loves" NP1, love(NP1)>

The subscripts link nonterminals across the two sides. When NP1 expands to "John"/john on both sides, the linking is maintained.

SCFG was widely used in statistical machine translation (Chiang, 2007) where the source and target were two natural languages. But the same mechanism works when the target is a logical language.

## Synchronous Tree-Adjoining Grammar

Synchronous TAG (Shieber and Schabes, 1990) extends the idea to tree-adjoining grammars. Paired elementary trees derive in parallel, with adjunction and substitution happening on both sides simultaneously.

This is more expressive than SCFG — it can handle certain long-distance dependencies and cross-serial dependencies that CFG cannot.

## Lambda Calculus as Target

Montague Grammar can be viewed as a synchronous grammar where:

- Source grammar: a context-free grammar for English
- Target grammar: a lambda calculus for logical forms
- Linking: each syntax rule has a corresponding semantic rule

    Syntax:  S → NP VP
    Semantics: VP'(NP')

    Syntax:  VP → TV NP
    Semantics: λx.TV'(x)(NP')

The derivation builds a parse tree and a lambda term in parallel. This is exactly the "rule-to-rule hypothesis" of Montague — every syntactic rule has a semantic counterpart.

## Relevance

Typed Slot Grammar is implicitly a synchronous grammar. Each rule has:

- A pattern (source): `$x:e COP A $P:{theme:e}`
- A template (target): `$P(theme: $x)`

The pattern matches surface tokens. The template builds a logical form. They derive in parallel, linked by shared variables ($x, $P). This is synchronous derivation with the two grammars collapsed into a single rule.

The difference from classical synchronous grammars: we do not have separate source and target nonterminals. There is no NP or VP on either side. The typed slots serve as both the source categories and the target placeholders. This collapsing is possible because we dispatch on semantic types rather than syntactic categories — the same type system serves both sides.

## Key References

- Shieber, S., & Schabes, Y. (1990). "Synchronous Tree-Adjoining Grammars"
- Chiang, D. (2007). "Hierarchical Phrase-Based Translation"
- Nesson, R., & Shieber, S. (2006). "Simpler TAG Semantics Through Synchronization"
- Wong, Y. W., & Mooney, R. (2006). "Learning for Semantic Parsing with Statistical Machine Translation"