# Experiments

## Dating Universe

A synthetic test with bipartite entities (jack/jill types).

Generative process:

- P(lonely(jack)) = 0.3
- P(exciting(jill)) = 0.6
- like(jack, jill) iff lonely(jack) OR exciting(jill)
- P(like(jill, jack)) = 0.4
- date(jack, jill) iff like(jack, jill) AND like(jill, jack)

## Results

Training on 4096 synthetic examples recovers the generative probabilities.

### Forward Inference

Evidence at roots propagates forward in one iteration.

### Backward Inference

Evidence at leaves propagates backward in O(depth) iterations.

## Message Propagation

Chain of predicates alpha_0, ..., alpha_N with N=10:

- Forward from alpha_0: converges in 1 iteration
- Backward from alpha_N: converges in 2N iterations

## vs Neural Approaches

The LRF:

1. Maintains P(x) + P(not x) = 1
2. Provides interpretable traces
3. Cannot hallucinate
4. Supports forward and backward inference

Tradeoff: requires structured logical forms (hence the parsing pipeline).