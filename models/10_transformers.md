# Transformers

## What is a Transformer?

The Transformer is a neural network architecture introduced by Vaswani et al. (2017) in "Attention Is All You Need." It relies entirely on attention mechanisms, dispensing with recurrence and convolutions.

Transformers are the foundation of:
- BERT, GPT, and all modern LLMs
- State-of-the-art NLP systems
- Many computer vision models (ViT)

## Architecture Overview

    Input Embeddings
          ↓
    (+ Positional Encoding)
          ↓
    ┌─────────────────┐
    │  Transformer    │ × N layers
    │     Block       │
    └─────────────────┘
          ↓
    Output

Each Transformer block contains:
1. Multi-head self-attention
2. Feed-forward network
3. Residual connections
4. Layer normalization

## The Transformer Block

    x ──→ [Self-Attention] ──→ Add & Norm ──→ [FFN] ──→ Add & Norm ──→ output
      └────────────────────────────┘      └──────────────────────────┘
              residual                           residual

**Self-Attention**: Each position attends to all positions
**FFN**: Two linear layers with ReLU/GELU activation
**Add & Norm**: Residual connection followed by layer normalization

## Positional Encoding

Self-attention is permutation-invariant — it doesn't know word order!

**Solution**: Add positional information to input embeddings.

**Sinusoidal encoding** (original):

    PE(pos, 2i) = sin(pos / 10000^(2i/d))
    PE(pos, 2i+1) = cos(pos / 10000^(2i/d))

**Learned positional embeddings**: Common in practice

**Rotary embeddings (RoPE)**: Encode relative positions in attention

## Encoder-Decoder vs. Decoder-Only

**Encoder-Decoder** (original Transformer):
- Encoder processes input with bidirectional attention
- Decoder generates output with causal (masked) attention
- Cross-attention connects decoder to encoder
- Used for: Translation, summarization

**Encoder-Only** (BERT):
- Bidirectional attention
- Used for: Classification, NER, sentence embeddings

**Decoder-Only** (GPT):
- Causal attention (can only see past)
- Used for: Language modeling, generation
- Modern LLMs (GPT-4, Claude) are decoder-only

## Causal Masking

In decoder-only models, position i can only attend to positions ≤ i.

    Attention mask (1 = can attend, 0 = blocked):
      1 2 3 4
    1 ✓ ✗ ✗ ✗
    2 ✓ ✓ ✗ ✗
    3 ✓ ✓ ✓ ✗
    4 ✓ ✓ ✓ ✓

This ensures the model can be trained in parallel while maintaining autoregressive generation.

## Training

**Pre-training objective**:
- Language modeling: Predict next token
- Masked language modeling: Predict masked tokens (BERT)

**Loss**: Cross-entropy over vocabulary

    L = -Σₜ log P(xₜ | x<t)

**Scale**: Modern LLMs train on trillions of tokens with billions of parameters.

## Emergent Capabilities

As transformers scale, they exhibit emergent behaviors:

- **In-context learning**: Learn from examples in the prompt
- **Chain-of-thought**: Reason step by step
- **Instruction following**: Generalize from instructions
- **Few-shot learning**: Adapt to new tasks with few examples

These capabilities were not explicitly trained — they emerge from scale.

## Limitations

Despite their success, transformers have fundamental limitations:

1. **No explicit reasoning**: Pattern matching, not logic
2. **Hallucinations**: Generate plausible but false content
3. **No world model**: Knowledge is implicit in weights
4. **Context window**: Limited memory (though growing)
5. **Expensive**: O(n²) attention, massive compute for training

## Relevance to QBBN

Transformers and QBBN are complementary:

| Aspect | Transformer/LLM | QBBN |
|--------|----------------|------|
| Strengths | Language understanding, flexibility | Reasoning, consistency |
| Weaknesses | Hallucination, no logic | Requires structured input |
| Knowledge | Implicit in weights | Explicit in KB |
| Inference | Forward pass | Belief propagation |

**The synthesis**:

    English → [Transformer] → Logical Form → [QBBN] → Inference

Use transformers for what they're good at (language), QBBN for what it's good at (reasoning).

## Key References

- Vaswani, A., et al. (2017). "Attention Is All You Need"
- Devlin, J., et al. (2018). "BERT: Pre-training of Deep Bidirectional Transformers"
- Radford, A., et al. (2018, 2019). "GPT" and "GPT-2"
- Brown, T., et al. (2020). "Language Models are Few-Shot Learners" (GPT-3)