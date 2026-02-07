# Logic and Probability

## The Integration Challenge

Logic and probability represent two modes of reasoning:

- **Logic**: Categorical, certain, symbolic
- **Probability**: Graded, uncertain, numerical

Combining them has been a long-standing goal in AI. The challenge: how do you add uncertainty to logical inference while maintaining tractability?

## Historical Approaches

### Probabilistic Logic (Nilsson, 1986)

Assign probabilities to logical sentences:

    P(bird(tweety)) = 0.9
    P(flies(tweety)) = ?

Problem: Doesn't scale — exponential number of possible worlds.

### Bayesian Logic Programs (Kersting & De Raedt, 2001)

Combine Bayesian networks with logic programs:
- Clauses define conditional dependencies
- Parameters learned from data

### Probabilistic Databases

Extend databases with uncertain tuples:

    bird(tweety): 0.9
    bird(polly): 0.7

Query: P(∃x. bird(x) ∧ flies(x)) = ?

## Markov Logic Networks (Richardson & Domingos, 2006)

The most influential recent approach.

**Idea**: A weighted first-order knowledge base where:
- Each formula has a weight
- Higher weight = stronger constraint
- Probability is proportional to exponentiated sum of satisfied weighted formulas

**Definition**:

    P(x) = (1/Z) exp(Σᵢ wᵢ nᵢ(x))

Where:
- wᵢ is the weight of formula i
- nᵢ(x) is the number of true groundings of formula i in world x
- Z is the partition function

**Example**:

    1.5: friends(x, y) ∧ smokes(x) → smokes(y)
    2.0: ¬smokes(x)

**Inference**: Grounding + Markov network inference (hard!)

**Limitations**:
- Inference is expensive (must ground everything)
- Can't handle infinite domains
- Weights are hard to interpret

## Probabilistic Logic Programming

**ProbLog** (De Raedt et al., 2007):

    0.3::stress(X) :- person(X).
    0.2::influences(X,Y) :- friend(X,Y).
    smokes(X) :- stress(X).
    smokes(X) :- friend(X,Y), influences(X,Y), smokes(Y).

Each clause has an independent probability of being "active."

**Inference**: Weighted model counting over possible programs.

## QBBN's Approach

QBBN takes a different route:

**1. Boolean variables for propositions**

Each grounded predicate is a boolean random variable:

    man(socrates) ∈ {true, false}
    mortal(socrates) ∈ {true, false}

**2. Factor graph structure from logical rules**

Rules define the factor graph structure:

    man(x) → mortal(x)

Becomes:

    man(socrates) → [AND] → group → [OR] → mortal(socrates)

**3. Noisy-OR for combining causes**

Multiple rules for the same conclusion combine probabilistically:

    P(mortal | causes) = 1 - ∏ᵢ (1 - wᵢ × causeᵢ)

**4. Belief propagation for inference**

Instead of weighted model counting or MCMC, use BP:
- Forward (π): causes to effects
- Backward (λ): effects to causes

**Advantages over MLN**:
- Efficient inference (BP vs. MCMC)
- Local structure (don't ground everything)
- Interpretable weights (noisy-OR semantics)
- Handles negation naturally (Ψ_neg factors)

## The Spectrum

| System | Logic | Probability | Inference |
|--------|-------|-------------|-----------|
| FOL | Full | None | Theorem proving |
| Bayesian Network | Propositional | Full | BP / exact |
| MLN | Full | Full | MCMC / lifted |
| ProbLog | Logic programs | Independent | Model counting |
| QBBN | Horn clauses | Noisy-OR | BP |

QBBN sacrifices some expressiveness (no full FOL) for tractable inference.

## Key References

- Nilsson, N. (1986). "Probabilistic Logic"
- Richardson, M. & Domingos, P. (2006). "Markov Logic Networks"
- De Raedt, L., et al. (2007). "ProbLog: A Probabilistic Prolog"
- Getoor, L. & Taskar, B. (2007). *Introduction to Statistical Relational Learning*