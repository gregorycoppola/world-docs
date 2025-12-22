# The Logical Random Field

## Random Fields

A probability distribution through local potentials:

    P(p1, ..., pn) = Z^-1 * product of Psi_alpha

Z is the partition function.

## Boolean Factors

We use boolean variables p in {0, 1} with two factor types:

### Conjunction (AND)

Deterministic. Creates higher-level features:

    Psi_and(g | p1, ..., pn) = 1 if g = p1 AND ... AND pn

### Disjunction (OR)

Learned log-linear model:

    Psi_or(p | g1, ..., gn) = exp(sum of w * phi(p, gi))

## Bipartite Structure

Graph alternates: AND -> OR -> AND -> OR

Enables efficient message passing.

## Implication Links

Quantified implications with learned weights:

    Psi[x, y]: like(x, y) -> date(x, y)

## Belief Propagation

Forward (pi) and backward (lambda) messages:

    P(z | evidence) = alpha * lambda(z) * pi(z)

Iterative BP converges well in practice.