# Application 2: Logical Reasoning

## The Problem

LLMs don't reason — they pattern match. While they can often produce correct-looking reasoning steps, they frequently fail on:

- **Modus ponens**: If A→B and A, then B
- **Modus tollens (contrapositive)**: If A→B and ¬B, then ¬A
- **Transitivity**: If A→B and B→C, then A→C
- **Conjunction**: A∧B is true iff both A and B are true
- **Disjunction**: A∨B is true iff at least one of A or B is true

Studies have shown that LLMs fail at basic syllogisms, especially when the content conflicts with common patterns in training data.

## How QBBN Performs Reasoning

QBBN performs reasoning via belief propagation on a factor graph. The key components:

### Forward Inference (π messages)

Information flows from premises to conclusions: