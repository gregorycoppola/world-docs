# Definite Clause Grammars

## Origins

Definite Clause Grammars (DCGs) were introduced by Pereira and Warren (1980) as a natural extension of Prolog for parsing. A DCG rule is a Prolog clause that consumes tokens from an input list while simultaneously building a semantic term.

## The Core Mechanism

A DCG rule looks like a grammar rule but acts like a logic program:

    s(love(X,Y)) --> np(X), vp(love(X,Y)).
    vp(love(X,Y)) --> v(love), np(Y).
    np(john) --> [john].
    np(mary) --> [mary].
    v(love) --> [loves].

Parsing "John loves Mary":

    s(love(john,mary))
      np(john) → [john]
      vp(love(john,mary))
        v(love) → [loves]
        np(mary) → [mary]

The syntactic parse and the semantic term are built in a single pass. There is no separate interpretation step. The term `love(john,mary)` falls out of unification during the parse.

## Key Properties

**Syntax and semantics are simultaneous.** Each rule consumes tokens (syntax) and builds a term (semantics) at the same time. The grammar IS the semantics.

**Rules can recurse.** An NP can contain a relative clause, which contains a VP, which contains an NP. DCGs handle this naturally because they are just Prolog — recursion is free.

**Unification does the binding.** Variables in the semantic term unify across rule invocations. When `np(X)` matches "John" and binds `X = john`, that binding propagates to `s(love(X,Y))` automatically.

**Backtracking handles ambiguity.** If one parse fails, Prolog backtracks and tries another. Multiple parses are explored systematically.

## Example: Relative Clauses

    np(X) --> det, n(X).
    np(X) --> det, n(X), rel(X).
    rel(X) --> [who], vp(P), { P =.. [_,X] }.

"The man who runs" parses as:

    np(X) → det:[the], n(X):[man], rel(X)
    rel(X) → [who], vp(runs(X))

The relative clause modifies the NP by adding a constraint on X. Recursion and unification handle this without special machinery.

## Limitations

**Efficiency.** Naive DCG parsing is exponential. Left recursion causes infinite loops. Practical systems need tabling or chart parsing.

**Overgeneration.** DCGs with unrestricted Prolog goals in the rules are Turing-complete. This makes formal analysis difficult.

**No probabilistic reasoning.** DCGs give yes/no parses. There is no natural way to rank parses or express uncertainty about which reading is correct.

## Relevance

DCGs are the closest existing formalism to Typed Slot Grammar. Both build syntax and semantics simultaneously. Both use pattern matching with backtracking. Both let the grammar rule directly specify the output logical form.

The key difference: DCGs dispatch on syntactic categories (s, np, vp). Typed Slot Grammar dispatches on semantic role types ({theme:e}, {agent:e,patient:e}). DCGs inherit recursion from Prolog. Typed Slot Grammar inherits type checking from the lexicon.

## Key References

- Pereira, F., & Warren, D. (1980). "Definite Clause Grammars for Language Analysis"
- Pereira, F., & Shieber, S. (1987). *Prolog and Natural-Language Analysis*
- Clocksin, W., & Mellish, C. (2003). *Programming in Prolog* (Chapter on DCGs)