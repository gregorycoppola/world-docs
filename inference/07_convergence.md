# Convergence

## The Convergence Question

Belief propagation is guaranteed to converge on tree-structured graphs. For graphs with cycles (loopy BP), convergence is not guaranteed but often works in practice.

## Tree-Structured Graphs

A factor graph is a tree if:
- Every variable has at most one path to every other variable
- Equivalently: no cycles when ignoring edge direction

For trees:
- BP converges in a single pass (forward then backward)
- Results are exact marginal probabilities

## Loopy Graphs

QBBN graphs can have cycles:

    trust(a, b) & trust(b, a) -> allies(a, b)
    allies(a, b) -> cooperate(a, b)
    cooperate(a, b) -> trust(a, b)

This creates a cycle: trust → allies → cooperate → trust

For loopy graphs:
- BP may not converge
- If it converges, results may not be exact
- But empirically, it often works well

## Damping

Damping helps convergence by smoothing updates:

    new_belief = α × old_belief + (1 - α) × computed_belief

Where α ∈ [0, 1] is the damping factor.

**α = 0**: No damping, use computed value directly
**α = 0.5**: Average old and new (our default)
**α = 0.9**: Mostly keep old value, slow updates

Higher damping:
- Slower convergence
- More stable (less oscillation)
- May get stuck in local optima

## Convergence Detection

We check convergence by comparing beliefs across iterations:

    max_change = max_z |belief(z)_new - belief(z)_old|
    
    if max_change < tolerance:
        converged = True

Typical tolerance: 1e-6

## Iteration Limits

We set a maximum number of iterations (default: 20) to prevent infinite loops.

In practice:
- Most queries converge in 2-5 iterations
- Simple chains converge in 1 pass
- Complex cyclic graphs may need 10-20 iterations

## Why QBBN Converges Well

Several properties help convergence:

### Bipartite Structure

The graph alternates: propositions → groups → propositions

This limits cycle structure — cycles must go through both node types.

### Deterministic AND Factors

AND factors are deterministic, which helps stability:
- No parameters to oscillate
- Clear signal propagation

### Bounded Factors

OR factors have bounded influence:
- Noisy-OR probabilities are in [0, 1]
- No extreme values that could cause instability

### Sparse Evidence

Typically few evidence nodes relative to total variables:
- Evidence provides strong anchoring
- Reduces degrees of freedom

## Failure Modes

### Oscillation

Beliefs oscillate between values without settling:

    iteration 1: P(x) = 0.3
    iteration 2: P(x) = 0.7
    iteration 3: P(x) = 0.3
    ...

**Solution**: Increase damping

### Slow Convergence

Beliefs change very slowly:

    iteration 1: P(x) = 0.500
    iteration 2: P(x) = 0.501
    iteration 3: P(x) = 0.502
    ...

**Solution**: Decrease damping, or accept approximate result

### Non-Convergence to Truth

BP converges but to wrong values.

This can happen in loopy graphs. The result is a "pseudo-marginal" that approximates but doesn't equal the true marginal.

**Mitigation**: For critical applications, verify with exact inference on small cases.

## Empirical Results

On our test suite (44 cases):
- Average iterations to converge: 2.3
- Maximum iterations needed: 8
- No non-convergence observed

The QBBN structure appears well-suited to BP convergence.

## Theoretical Guarantees

**For trees**: Exact inference, guaranteed convergence.

**For single-cycle graphs**: Known bounds on error.

**For general graphs**: No guarantees, but extensive empirical success across many domains (computer vision, NLP, error correction codes).

## References

- Murphy, K., Weiss, Y., & Jordan, M. (1999). "Loopy Belief Propagation for Approximate Inference"
- Yedidia, J., Freeman, W., & Weiss, Y. (2003). "Understanding Belief Propagation and its Generalizations"
- Mooij, J. & Kappen, H. (2007). "Sufficient Conditions for Convergence of Loopy Belief Propagation"