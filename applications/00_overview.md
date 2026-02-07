# Applications Overview

## The Five Problems

Large Language Models have achieved remarkable capabilities in natural language understanding and generation. However, they suffer from five fundamental limitations that prevent their use in high-stakes reasoning tasks:

1. **Hallucinations** — LLMs generate plausible but false statements
2. **Reasoning** — LLMs struggle with multi-step logical inference
3. **Planning** — LLMs cannot reliably work backward from goals
4. **Continuous Learning** — LLMs cannot update knowledge without retraining
5. **World Models** — LLMs lack explicit, inspectable representations

QBBN addresses each of these problems through its architecture.

## Why These Problems Exist

LLMs are trained to predict the next token. This objective creates systems that are:

- **Statistically plausible** but not **logically sound**
- **Pattern matchers** rather than **reasoners**
- **Black boxes** without **inspectable state**
- **Frozen** at training time

## The QBBN Solution

QBBN takes a different approach:

| Problem | LLM Behavior | QBBN Solution |
|---------|--------------|---------------|
| Hallucinations | Generate plausible text | Only assert what follows from evidence |
| Reasoning | Pattern match to training data | Belief propagation over factor graph |
| Planning | Generate action sequences | Backward inference from goals |
| Learning | Requires retraining | Update KB directly |
| World Models | Implicit in weights | Explicit factor graph |

## Architecture

The system has three main components:

### 1. Parser (LLM-assisted)

Converts natural language to logical form:

    "Socrates is a man" → man(theme: socrates)
    "All men are mortal" → always [x:e]: man(theme: x) -> mortal(theme: x)

### 2. Knowledge Base

Stores facts and rules in logical form:

    Facts: man(socrates), philosopher(socrates)
    Rules: man(x) -> mortal(x), philosopher(x) -> thinker(x)

### 3. Inference Engine (QBBN)

Computes answers via belief propagation:

    Query: mortal(socrates)?
    Result: P = 1.0 (yes), derived from man(socrates) + rule

## The Key Insight

LLMs are good at language, bad at reasoning. Symbolic systems are good at reasoning, bad at language.

QBBN combines them:

    Language understanding → LLM
    Logical reasoning → QBBN
    Language generation → LLM

Each component does what it's best at.

## Document Structure

The following documents detail how QBBN addresses each problem:

- `01_hallucinations.md` — Grounded assertions with provenance
- `02_reasoning.md` — Sound inference via belief propagation
- `03_planning.md` — Backward inference for goal-directed behavior
- `04_continuous_learning.md` — Incremental KB updates