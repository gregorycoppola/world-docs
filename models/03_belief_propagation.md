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