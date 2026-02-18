# Computability and First-Order Logic

## The Equivalence

First-order logic and Turing machines are computationally equivalent. Each can simulate the other, and neither can do more than the other. This equivalence is not a coincidence — it reflects the fact that both formalisms were discovered as answers to the same question: what are the boundaries of mechanical reasoning?

## A Turing Machine Can Implement a First-Order Theorem Prover

This direction follows from Gödel's completeness theorem (1929), which established that every valid first-order sentence has a finite proof. Since the set of valid sentences is recursively enumerable, there exists a mechanical procedure that will find a proof for any valid sentence in finite time. A Turing machine can systematically enumerate all possible proofs — for instance by enumerating all finite sequences of rule applications in a sound proof system — and check each one for correctness.

In practice, no one implements provers by brute-force proof enumeration. Real theorem provers use far more sophisticated strategies — resolution, tableaux, superposition — but the theoretical point is that a Turing machine is more than powerful enough to execute any of these algorithms.

## A First-Order Theorem Prover Can Simulate Any Turing Machine

Given any Turing machine and any input, you can encode the machine's computation as a set of first-order axioms and then pose a query to the prover that will be provable if and only if the machine halts on that input.

The encoding works by reifying the machine's execution trace as logical predicates:

- `State(t, q)` — at time step t, the machine is in state q
- `Head(t, p)` — at time step t, the read/write head is at position p
- `Tape(t, p, s)` — at time step t, the symbol at position p is s

The initial configuration — start state, head position, input tape — is expressed as ground facts. Each transition rule becomes a universally quantified axiom. For example, a rule saying "in state q₁, reading symbol s₁, write s₂, move right, go to state q₂" becomes:

    ∀t,p. State(t, q₁) ∧ Head(t, p) ∧ Tape(t, p, s₁) →
          State(t+1, q₂) ∧ Head(t+1, p+1) ∧ Tape(t+1, p, s₂)

Frame axioms state that tape cells not under the head remain unchanged between steps.

With this encoding, the query `∃t. State(t, halt)` is provable if and only if the Turing machine halts. If it halts, the prover reconstructs the execution trace as a chain of logical deductions. If it does not halt, the prover searches forever.

## The Bidirectional Result

Taken together, these two directions yield a single equivalence: a problem is Turing-recognizable (recursively enumerable) if and only if it is reducible to the validity problem in first-order logic. The set of provable FOL sentences and the set of halting Turing machines are the same kind of object — they sit at exactly the same level in the arithmetical hierarchy (Σ₁⁰), and each is a complete problem for that level.

Both are semi-decision procedures. The theorem prover will confirm any valid sentence but may run forever on invalid ones. The Turing machine simulation will confirm halting but may run forever on non-halting inputs. These are not two different limitations — they are the same limitation, viewed from two sides. The undecidability of first-order validity and the undecidability of the halting problem are, through this equivalence, the same theorem.

## The Church-Turing Thesis

The Church-Turing thesis is the claim that every function which is effectively computable — meaning computable by any systematic, mechanical procedure — is computable by a Turing machine. It is not a mathematical theorem but a philosophical claim about the nature of computation itself, and it has never been refuted.

Its force comes from a remarkable convergence. In the 1930s, several independent formalizations of computability were proposed — Turing machines, Church's lambda calculus, Gödel and Herbrand's recursive functions, Post's production systems, Markov algorithms — and they all turned out to define exactly the same class of computable functions. First-order theorem proving falls into this same equivalence class. Every formalism anyone has ever proposed for capturing the intuitive notion of mechanical computation has turned out to be equivalent to all the others, or weaker. None has ever turned out to be strictly stronger.

The thesis applies to any process that is deterministic, proceeds in discrete steps, operates on finite (though unbounded) representations, and requires no insight, creativity, or infinite precision — in short, anything you could specify as an unambiguous set of instructions. This covers all conventional digital computation, every programming language, every algorithm, every formal proof search.

What the thesis does not cover is worth noting. It says nothing about efficiency — a problem may be computable but require more time than the age of the universe. It does not address computation over continuous quantities. And it does not settle whether physical processes in nature might exceed Turing computability — though no convincing example has ever been demonstrated.

## Relevance to QBBN

This equivalence has a direct implication for the design of QBBN: choosing logic as the language of thought is not a restriction. FOL captures all of mechanical reasoning. Any computation that can be performed at all can be encoded as logical inference.

The real design question is not whether logic is powerful enough, but which *fragment* of logic to use. Full first-order theorem proving is Turing-complete — and therefore undecidable. The QBBN deliberately restricts to the forward fragment identified in Prawitz's natural deduction (see `11_prawitz_natural_deduction.md`): the simple elimination rules realized as AND/OR factors with belief propagation. This fragment is tractable — linear time, convergent in 2–3 iterations on all test cases.

The fragment hierarchy from Prawitz maps directly onto the computability landscape:

| Fragment | Prawitz Rules | Complexity | QBBN Status |
|----------|--------------|------------|-------------|
| Forward | Simple elimination only | Linear time | Implemented |
| Query | + unsafe ∀-Elim, ∃-Intro | Existential search | Planned |
| Planning | + ∨-Elimination | NP-hard | Future |
| Full theorem proving | + →-Intro, ∀-Intro, ∃-Elim | Undecidable (Turing-complete) | Out of scope |

The computability equivalence explains why full theorem proving is undecidable — it can encode arbitrary Turing machine computations — and why the QBBN's restriction to the forward fragment is a principled choice: it trades expressiveness for guaranteed tractability, while retaining enough power for the natural language reasoning tasks the system targets.

## Future Directions

The relationship between logic and computation connects to a broader question about the structure of natural language itself. Chomsky's hypothesis of a universal grammar — an innate computational system underlying all human languages — suggests that the human language faculty operates within specific computational bounds. Understanding where those bounds sit relative to the computability hierarchy, and how the QBBN's chosen fragment relates to the complexity of natural language, is a question we return to in later sections.

## Key References

- Turing, A. (1936). "On Computable Numbers, with an Application to the Entscheidungsproblem." *Proceedings of the London Mathematical Society*, 42:230–265.
- Church, A. (1936). "An Unsolvable Problem of Elementary Number Theory." *American Journal of Mathematics*, 58:345–363.
- Gödel, K. (1929). *Über die Vollständigkeit des Logikkalküls*. PhD thesis, University of Vienna.
- Fagin, R. (1974). "Generalized First-Order Spectra and Polynomial-Time Recognizable Sets." *SIAM-AMS Proceedings*, 7:43–73.
- Immerman, N. (1999). *Descriptive Complexity*. Springer.