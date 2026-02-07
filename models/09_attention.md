# Attention Mechanisms

## What is Attention?

Attention is a mechanism that allows a model to focus on relevant parts of the input when producing an output. Instead of compressing all information into a fixed-size vector, attention lets the model "look back" at the input selectively.

Attention is the key innovation behind transformers and modern LLMs.

## The Problem Attention Solves

**Sequence-to-sequence models** (e.g., translation):

    Encoder: "The cat sat on the mat" → [fixed vector]
    Decoder: [fixed vector] → "Le chat s'est assis sur le tapis"

**Problem**: The fixed vector is a bottleneck. Long sequences lose information.

**Solution**: Let the decoder attend to different parts of the input at each step.

## Bahdanau Attention (2014)

The original attention mechanism for neural machine translation.

**Idea**: At each decoder step, compute a weighted sum of encoder states.

    context_t = Σᵢ αₜᵢ × hᵢ

Where:
- hᵢ is the i-th encoder hidden state
- αₜᵢ is the attention weight (how much to focus on position i at time t)

**Computing attention weights**:

    eₜᵢ = score(sₜ₋₁, hᵢ)           # alignment score
    αₜᵢ = softmax(eₜᵢ)              # normalize to sum to 1

The score function can be:
- Dot product: sᵀh
- Additive: vᵀtanh(Ws + Uh)
- Multiplicative: sᵀWh

## Self-Attention

Instead of attending to a separate encoder, attend to other positions in the same sequence.

    Input: "The cat sat on the mat"
    Output: For each word, a weighted combination of all words

This allows modeling long-range dependencies without recurrence.

## Scaled Dot-Product Attention

The attention mechanism used in transformers.

**Inputs**:
- Q (queries): What we're looking for
- K (keys): What we're matching against
- V (values): What we retrieve

**Computation**:

    Attention(Q, K, V) = softmax(QKᵀ / √dₖ) × V

Where dₖ is the dimension of the keys (scaling prevents softmax saturation).

**Matrix form** (for efficiency):
- Q, K, V are matrices where each row is a query/key/value
- Output is a matrix where each row is the attended value for that query

## Multi-Head Attention

Instead of one attention function, use multiple "heads" in parallel:

    MultiHead(Q, K, V) = Concat(head₁, ..., headₕ) × W^O
    
    Where headᵢ = Attention(QWᵢQ, KWᵢK, VWᵢV)

**Why multiple heads?**
- Different heads can attend to different things
- One head might track syntax, another semantics
- Increases representational capacity

## Attention Patterns

What do attention weights look like?

**Position attention**: Attend to nearby positions
**Content attention**: Attend to semantically related words
**Syntactic attention**: Attend along parse tree edges

Example: In "The cat that I saw yesterday ran away"
- "ran" attends strongly to "cat" (its subject)
- "saw" attends strongly to "cat" (its object)
- "yesterday" attends to "saw" (temporal modifier)

## Computational Complexity

Standard attention: O(n²) in sequence length n
- Every position attends to every other position
- Problematic for long sequences

**Efficient attention variants**:
- Sparse attention: Only attend to subset of positions
- Linear attention: Approximate softmax to get O(n)
- Sliding window: Only attend to local context

## Relevance to QBBN

Attention and QBBN share a key-value structure:

**Attention**:

    Query: "What should I focus on?"
    Keys: "Here are the options"
    Values: "Here's what you get"
    Output: Weighted sum of values based on query-key match

**QBBN predicates**:

    predicate(role₁: value₁, role₂: value₂, ...)

The role labels are like keys, the values are like... values.

**Connection**: Vaswani et al. (2017) noted that attention "can be described as mapping a query and a set of key-value pairs to an output."

Our logical language uses explicit role-value structure. Attention uses learned soft role-value structure.

## Key References

- Bahdanau, D., Cho, K., & Bengio, Y. (2014). "Neural Machine Translation by Jointly Learning to Align and Translate"
- Luong, M.-T., Pham, H., & Manning, C. D. (2015). "Effective Approaches to Attention-based Neural Machine Translation"
- Vaswani, A., et al. (2017). "Attention Is All You Need"