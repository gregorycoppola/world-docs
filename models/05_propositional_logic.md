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