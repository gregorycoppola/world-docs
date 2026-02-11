# Prawitz: Natural Deduction

## Source

Prawitz, D. (1965). *Natural Deduction: A Proof-Theoretical Study*. Almqvist & Wiksell.

Originally Prawitz's PhD dissertation. The definitive treatment of natural deduction as a proof-theoretic framework, extending Gentzen's (1934) original system.

## What the Book Covers

- **Chapters I–IV**: First-order natural deduction. Introduction and elimination rules for all connectives (∧, ∨, →, ∀, ∃, ⊥). The simple/improper distinction. Normal form theorems — every deduction can be simplified to one without detours.
- **Chapter V**: Second-order logic. Predicate variables, second-order quantification (∀X, ∃X), and lambda abstraction as a primitive. Ramified vs. simple second order — ramified is tractable, simple is undecidable.
- **Chapter VI**: Modal logic. The N (necessity) operator, NI/NE rules, essentially modal formulas. Covers systems S4 and S5.
- **Chapter VII**: Relevant implication. Restricting → so premises must actually be used in the deduction — not everything follows from anything.

## The Introduction/Elimination Rules

Prawitz's system has two kinds of rules for each logical connective:

**Introduction rules** — how to establish a connective (build up).
**Elimination rules** — how to use a connective (break down).

### First-Order Rules

| Connective | Introduction | Elimination |
|------------|-------------|-------------|
| ∧ (AND) | From A and B, conclude A ∧ B | From A ∧ B, conclude A (or B) |
| ∨ (OR) | From A, conclude A ∨ B | From A ∨ B, if A→C and B→C, conclude C |
| → (IMPLIES) | If assuming A lets you prove B, conclude A → B | From A and A → B, conclude B (modus ponens) |
| ∀ (FORALL) | If you proved A without referencing any particular x, conclude ∀x.A | From ∀x.A, conclude A[t/x] for any term t |
| ∃ (EXISTS) | From A[t/x] for some term t, conclude ∃x.A | From ∃x.A, assume A[c/x] for fresh c, reason from there |
| ⊥ (ABSURD) | From A and ¬A, conclude ⊥ | From ⊥, conclude anything |

## The Simple/Complex Distinction

This is the key insight for our system. Prawitz classifies rules as:

**Simple (proper) rules** — the conclusion follows directly from the premises. No assumptions are discharged, no search is required.

**Complex (improper) rules** — the rule discharges an assumption or requires reasoning under a hypothetical. These are harder computationally.

### Simple Rules (Forward Fragment)

- ∧-Introduction: have A and B, get A ∧ B
- ∧-Elimination: have A ∧ B, get A
- ∨-Introduction: have A, get A ∨ B
- →-Elimination: have A and A → B, get B (modus ponens)
- ∀-Elimination (safe): have ∀x.A, get A[t/x] for known t
- ∃-Introduction: have A[t/x], get ∃x.A

All of these are mechanical. Given the premises, the conclusion is determined. No search, no case analysis, no hypothetical reasoning.

### Complex Rules (Query and Planning Fragments)

- →-Introduction: assume A, prove B, conclude A → B (discharges assumption)
- ∀-Introduction: prove A for arbitrary x, conclude ∀x.A (requires generalization)
- ∨-Elimination: have A ∨ B, prove C from A, prove C from B, conclude C (case analysis)
- ∃-Elimination: have ∃x.A, assume A[c/x] for fresh c (requires witness search)

These require search, backtracking, or reasoning under hypotheticals. They are what make full theorem proving undecidable.

## Mapping to the QBBN Factor Graph

The QBBN implements the simple rules as factor types:

| Prawitz Rule | QBBN Factor | Direction |
|-------------|-------------|-----------|
| →-Elimination (modus ponens) | Ψ_and → Ψ_or (forward π messages) | Forward |
| ∧-Introduction | Ψ_and: group is true iff all premises true | Forward |
| ∧-Elimination | Implicit in graph structure | Forward |
| ∨-Introduction | Ψ_or: multiple groups support same conclusion | Forward |
| Contrapositive (¬B, A→B ⊢ ¬A) | Ψ_neg + backward λ messages | Backward |

The NEG factor is the key innovation beyond standard natural deduction: it links P and ¬P, enforcing P(X) + P(¬X) = 1, which enables modus tollens through belief propagation. When negative evidence flows backward through the OR and AND factors, it constrains premises — this is contrapositive reasoning emerging from the message-passing structure.

## The Fragment Hierarchy

The simple/complex distinction produces a hierarchy of computational fragments:

**Forward fragment** (simple elimination rules only) — Linear time.
This is what the QBBN currently implements. Facts fire rules, rules produce conclusions, conclusions fire more rules. Every step is determined. Belief propagation converges in 2–3 iterations.

**Query fragment** (adds unsafe ∀-Elimination, ∃-Introduction) — Existential search.
"Is there someone who loves Mary?" requires searching over entities. The noisy-OR over entities handles this, but the search space grows with the domain.

**Planning fragment** (adds ∨-Elimination) — NP-hard.
Reasoning by cases: "Either A or B; if A then C; if B then C; therefore C." Requires exploring both branches.

**Full theorem proving** (adds →-Introduction, ∀-Introduction, ∃-Elimination) — Undecidable.
Proving things for all cases, constructing implications, reasoning from existential witnesses.

The QBBN deliberately stays in the forward fragment. That is why it is tractable and why belief propagation converges fast on all 44 test cases.

## Second-Order Logic (Chapter V)

Prawitz extends natural deduction to second order by adding three things:

**1. Predicate variables**: Variables X that range over predicates, not individuals. Where first-order ∀x quantifies over entities, second-order ∀X quantifies over properties.

**2. Second-order quantification**: ∀X.A ("for all properties X, A holds") and ∃X.A ("there exists a property X such that A holds").

**3. Lambda abstraction**: λx₁...xₙ.A is an n-place predicate term. Lambda is a primitive, not syntactic sugar. Prawitz calls λ the "principal sign" of a predicate term.

### Lambda Introduction and Elimination

- **λI**: From formula A with free variables x₁...xₙ, form λx₁...xₙ.A
- **λE**: Apply λx₁...xₙ.A to terms t₁...tₙ, get A[t₁/x₁, ..., tₙ/xₙ]

Both are simple/forward operations — mechanical substitution with no search.

### Ramified vs. Simple

**Simple second order**: Unrestricted quantification over predicates. Undecidable.

**Ramified second order**: Predicates have levels. A level-m predicate can only quantify over predicates of level m-1 or lower. More tractable — blocks circular self-reference.

### Relevance to QBBN

In our system:
- `man` is a predicate constant (first order)
- `λx. man(x) & tall(x)` would be a predicate term (second order, via λI)
- `is_a(theme: john, class: man)` uses a predicate in a role position
- `want(agent: x, content: P)` where P ranges over propositions is second-order quantification

Lambda composition and decomposition are forward fragment operations — cheap. Existential quantification over predicates (∃P: P(john) & P(mary), "John and Mary have something in common") belongs in the query fragment — expensive.

## Modal Logic (Chapter VI)

Prawitz adds the necessity operator N:

**NI** (Necessity Introduction): From A, conclude NA — but only when all assumptions are themselves modal. This is a complex/improper rule.

**NE** (Necessity Elimination): From NA, conclude A. No restriction. Simple/proper rule.

**Essentially modal formulas**: A formula where every predicate stands within the scope of N. Prawitz proves: if A is essentially modal, then A ⊃ NA is provable.

### Connection to Kripke Semantics

Prawitz gives the proof theory (what you can derive). Kripke (1959–1963) gives the model theory (what it means). They agree for S4 and S5:

- Kripke type: formula is a function from possible worlds to truth values
- □A (necessarily A): true at world w iff A is true at all accessible worlds
- ◇A (possibly A): true at world w iff A is true at some accessible world

### Relevance to QBBN

Modal operators map to intensional verbs: believe, want, can, must. These create contexts where propositions are entertained without being asserted.

- `believe(agent: john, content: mortal(socrates))` — John believes it, but it's not asserted as fact
- The content role holds a proposition under a modal operator
- NE (necessity elimination) is forward/simple: if something is necessarily true, it's true
- NI (necessity introduction) is complex: proving something is necessarily true requires checking all contexts

## Relevant Implication (Chapter VII)

Prawitz restricts → so that A must be actually *used* in deducing B for A → B to hold. In standard logic, P → (Q → P) is valid — anything implies itself regardless of context. In relevant logic, this fails because Q is never used.

For the QBBN, this means: rules should only fire when premises are genuinely relevant to conclusions. This tightens inference and prevents spurious derivations.

## Why Prawitz Matters for This Project

Prawitz provides the proof-theoretic foundation for the entire system:

1. **The inference engine** implements his forward fragment — the simple elimination rules realized as AND/OR factors with belief propagation
2. **The logical language** is a typed, role-labeled realization of his first-order system, with a clear extension path through second-order (lambda, predicate variables) and modal (necessity, possibility)
3. **The fragment hierarchy** explains why the system is tractable (forward fragment = linear time) and where future extensions sit (query = existential search, planning = NP-hard)
4. **The NEG factor** extends Prawitz by enabling contrapositive reasoning within belief propagation, which his original natural deduction handles through separate proof rules

## Key References

- Prawitz, D. (1965). *Natural Deduction: A Proof-Theoretical Study*. Almqvist & Wiksell.
- Gentzen, G. (1934). "Untersuchungen über das logische Schließen." *Mathematische Zeitschrift*, 39:176–210, 405–431.
- Kripke, S. (1963). "Semantical Considerations on Modal Logic." *Acta Philosophica Fennica*, 16:83–94.
- Simpson, A. (1994). *The Proof Theory and Semantics of Intuitionistic Modal Logic*. PhD thesis, University of Edinburgh.