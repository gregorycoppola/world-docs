# Propositional Logic

## What is Propositional Logic?

Propositional logic is the simplest form of logic, dealing with propositions (statements that are either true or false) and logical connectives.

It's the foundation for:
- Boolean algebra and digital circuits
- SAT solvers
- The boolean variables in QBBN

## Syntax

**Propositions**: Atomic statements, usually denoted p, q, r, ...

**Connectives**:
- ¬ (NOT, negation): ¬p is true iff p is false
- ∧ (AND, conjunction): p ∧ q is true iff both p and q are true
- ∨ (OR, disjunction): p ∨ q is true iff at least one of p, q is true
- → (IMPLIES, implication): p → q is true iff ¬p ∨ q
- ↔ (IFF, biconditional): p ↔ q is true iff p and q have the same value

**Formulas**: Built recursively from propositions and connectives

    φ ::= p | ¬φ | φ ∧ φ | φ ∨ φ | φ → φ | φ ↔ φ

## Semantics

A **truth assignment** is a function v: Propositions → {T, F}

Truth tables define the semantics:

    p | ¬p
    T | F
    F | T
    
    p | q | p∧q | p∨q | p→q | p↔q
    T | T |  T  |  T  |  T  |  T
    T | F |  F  |  T  |  F  |  F
    F | T |  F  |  T  |  T  |  F
    F | F |  F  |  F  |  T  |  T

## Key Concepts

**Satisfiability**: A formula φ is satisfiable if there exists a truth assignment that makes it true.

**Validity (tautology)**: A formula φ is valid if all truth assignments make it true.

**Entailment**: φ ⊨ ψ means every assignment satisfying φ also satisfies ψ.

**Equivalence**: φ ≡ ψ means φ ⊨ ψ and ψ ⊨ φ.

## Important Equivalences

**De Morgan's Laws**:

    ¬(p ∧ q) ≡ ¬p ∨ ¬q
    ¬(p ∨ q) ≡ ¬p ∧ ¬q

**Implication**:

    p → q ≡ ¬p ∨ q

**Contrapositive**:

    p → q ≡ ¬q → ¬p

**Double Negation**:

    ¬¬p ≡ p

## Normal Forms

**Conjunctive Normal Form (CNF)**: Conjunction of disjunctions

    (p ∨ q ∨ ¬r) ∧ (¬p ∨ r) ∧ (q ∨ r)

**Disjunctive Normal Form (DNF)**: Disjunction of conjunctions

    (p ∧ q) ∨ (¬p ∧ r) ∨ (q ∧ ¬r)

Any formula can be converted to CNF or DNF.

## SAT Problem

The **Boolean Satisfiability Problem (SAT)**: Given a formula φ (usually in CNF), is there a satisfying assignment?

- First problem proven NP-complete (Cook, 1971)
- Modern SAT solvers can handle millions of variables
- Used in verification, planning, cryptography

## Proof Systems

**Natural Deduction**: Introduction and elimination rules for each connective

**Resolution**: A single rule that's complete for refutation

    (p ∨ A) ∧ (¬p ∨ B) ⊢ (A ∨ B)

**Sequent Calculus**: Gentzen's system with sequents Γ ⊢ Δ

## Relevance to QBBN

QBBN's propositions are propositional logic variables:

- Each grounded predicate `man(theme: socrates)` is a boolean variable
- AND factors compute conjunction
- OR factors compute (noisy) disjunction
- Negation factors enforce ¬p + p = 1

The key extension: QBBN adds **probability** to propositional logic.

Instead of just true/false, we have P(p = true) ∈ [0, 1].

The noisy-OR generalizes disjunction:
- Deterministic OR: p = q₁ ∨ q₂ ∨ ... ∨ qₙ
- Noisy-OR: P(p | q₁, ..., qₙ) = 1 - ∏ᵢ (1 - wᵢ × qᵢ)

## Key References

- Enderton, H. B. (2001). *A Mathematical Introduction to Logic*
- Huth, M. & Ryan, M. (2004). *Logic in Computer Science*
- Biere, A., et al. (2009). *Handbook of Satisfiability*