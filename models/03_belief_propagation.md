# Belief Propagation

## What is Belief Propagation?

Belief Propagation (BP) is a message-passing algorithm for performing inference in graphical models. It computes marginal distributions by passing "messages" between nodes in the graph.

For tree-structured graphs, BP gives exact results. For graphs with cycles ("loopy" graphs), it's an approximation that often works well in practice.

## The Two Directions

BP involves two types of messages, corresponding to two directions of information flow:

**π messages (forward)**: Beliefs flowing from causes to effects
- "Given what I know about the causes, here's what I believe about the effect"

**λ messages (backward)**: Beliefs flowing from effects to causes
- "Given what I observed about the effects, here's what I believe about the causes"

## Pearl's Algorithm

For a node X with parents U₁, ..., Uₘ and children Y₁, ..., Yₖ:

### Values

**π(X)**: Forward belief about X from its parents

    π(X = x) = Σᵤ P(X = x | U = u) ∏ᵢ πₓ(uᵢ)

**λ(X)**: Backward belief about X from its children

    λ(X = x) = ∏ⱼ λYⱼ(x)

### Messages

**πᵧ(X)**: Message from X to child Y

    πᵧ(X = x) = π(X = x) × ∏_{Y' ≠ Y} λY'(x)

**λᵧ(X)**: Message from child Y to X

    λᵧ(X = x) = Σᵧ [Σᵤ P(Y = y | X = x, U = u) ∏ᵢ πᵧ(uᵢ)] × λ(Y = y)

### Belief

The final belief (posterior probability) is:

    P(X = x | evidence) ∝ π(X = x) × λ(X = x)

## Example: Chain

Consider a simple chain: A → B → C

If we observe C = true:

1. Initialize: λ(C = true) = 1, λ(C = false) = 0
2. λ message from C to B: λC(B) based on P(C | B)
3. λ message from B to A: λB(A) based on P(B | A)
4. Beliefs update: P(A | C) ∝ π(A) × λB(A)

The observation propagates backward through the chain.

## Loopy Belief Propagation

When the graph has cycles, exact BP doesn't apply. Instead, we use **iterative** (loopy) belief propagation:

1. Initialize all messages to uniform
2. Repeat until convergence:
   - Update all π messages (forward pass)
   - Update all λ messages (backward pass)
   - Compute beliefs
3. Check for convergence (beliefs stop changing)

**Properties**:
- Not guaranteed to converge
- Not guaranteed to be correct even if it converges
- But empirically works well in many applications

## Message Schedules

The order of message updates matters:

**Synchronous**: Update all messages simultaneously
**Asynchronous**: Update one message at a time
**Residual BP**: Update the message with largest change first

Different schedules can affect convergence speed and quality.

## Damping

To improve convergence, we often use **damping**:

    new_message = α × old_message + (1 - α) × computed_message

Where α ∈ [0, 1] is the damping factor. This prevents oscillation in loopy graphs.

## Complexity

For a single pass of BP:
- Time: O(N × d^k) where N is number of nodes, d is domain size, k is max factor size
- The exponential in k comes from summing over all configurations of a factor

For QBBN:
- d = 2 (boolean)
- k bounded by number of premises in a rule
- So O(N × 2^k) per pass

## Connection to Other Algorithms

BP is related to many other algorithms:

- **Forward-backward** (HMMs): BP on a chain
- **Viterbi**: Max-product BP (replace sum with max)
- **Junction tree**: Exact BP on cluster graph
- **Variational inference**: BP as coordinate descent on a free energy

## Relevance to QBBN

QBBN uses Pearl's BP with:

1. **π messages**: Flow through AND gates (all premises true → group true) and OR gates (any group true → conclusion true)

2. **λ messages**: Flow backward for contrapositive reasoning (conclusion false → premises constrained)

3. **Evidence handling**: Observed nodes have fixed π and λ based on their value

4. **Iterative**: We run multiple passes until convergence

The key insight: proper π/λ separation enables backward reasoning (contrapositive) that a pure forward model misses.

## Key References

- Pearl, J. (1988). *Probabilistic Reasoning in Intelligent Systems*
- Murphy, K., Weiss, Y., & Jordan, M. (1999). "Loopy Belief Propagation for Approximate Inference"
- Yedidia, J., Freeman, W., & Weiss, Y. (2003). "Understanding Belief Propagation and its Generalizations"