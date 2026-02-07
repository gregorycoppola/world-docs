# Hallucinations

## The Problem

LLMs hallucinate — they generate statements that are plausible-sounding but false. This includes:

- **Fabricated facts**: "The Eiffel Tower was built in 1823"
- **False citations**: Invented paper titles, authors, DOIs
- **Confident errors**: Wrong answers stated with high confidence
- **Plausible nonsense**: Grammatically correct but semantically false

### Why LLMs Hallucinate

LLMs are trained to maximize P(next token | context). This objective rewards:

- Fluency over accuracy
- Plausibility over truth
- Confidence over calibration

The model has no explicit notion of "truth" — only statistical patterns.

### The Danger

Hallucinations are particularly dangerous because:

1. They're often indistinguishable from correct answers
2. They're stated with the same confidence as true facts
3. Users may not have expertise to verify
4. There's no way to know why it gave that answer

## The QBBN Solution

In QBBN, every assertion has a derivation:

    Assertion: mortal(socrates)
    Derivation:
      1. man(socrates) [evidence, from input]
      2. man(x) -> mortal(x) [rule, from KB]
      3. mortal(socrates) [derived, via modus ponens]

### Grounded Assertions

QBBN only asserts what follows from:

- **Evidence**: Explicitly provided facts
- **Rules**: Explicitly provided implications
- **Inference**: Sound logical derivation

There's no mechanism to generate "plausible but unsupported" claims.

### Provenance Tracking

Every belief has a trace:

    Query: Is Socrates mortal?
    Answer: Yes (P = 1.0)
    
    Provenance:
    - Fact: man(theme: socrates) [source: input text]
    - Rule: always [x:e]: man(theme: x) -> mortal(theme: x) [source: KB]
    - Inference: Applied rule to fact

### Calibrated Uncertainty

When evidence is incomplete, QBBN reports uncertainty:

    Query: Is Aristotle mortal?
    
    If man(aristotle) not in KB:
      Answer: Unknown (P = 0.5)
    
    If man(aristotle) in KB:
      Answer: Yes (P = 1.0)

The system distinguishes "I don't know" from "yes" and "no".

## Comparison

| Aspect | LLM | QBBN |
|--------|-----|------|
| Assertion basis | Statistical patterns | Logical derivation |
| Confidence | Often miscalibrated | Reflects evidence |
| Provenance | None | Full trace |
| "I don't know" | Rarely says this | Explicit uncertainty |
| Verification | Requires external check | Self-documenting |

## Example: Citation

**LLM behavior**:

    User: What paper introduced attention?
    LLM: "Attention Is All You Need" by Vaswani et al. (2017)
    
    (Correct, but LLM could equally confidently give wrong answer)

**QBBN behavior**:

    Query: paper_introduced(mechanism: attention)?
    
    If KB contains:
      paper(title: "Attention Is All You Need", author: vaswani, year: 2017)
      paper_introduced(paper: attention_paper, mechanism: attention)
    
    Answer: "Attention Is All You Need" by Vaswani et al. (2017)
    Provenance: [explicit KB entries]
    
    If KB doesn't contain this:
    Answer: Unknown
    Explanation: No information about attention mechanism origin in KB

## Limitations

QBBN prevents hallucination but has its own limitations:

1. **Coverage**: Can only answer what's in KB
2. **Parsing errors**: LLM parser might introduce errors
3. **KB errors**: If KB contains false facts, QBBN will propagate them

The solution shifts the problem from "uncontrolled generation" to "KB curation" — which is more tractable.

## Key Insight

Hallucination is fundamentally a problem of **ungrounded generation**. QBBN solves it by requiring every assertion to be **grounded in evidence and derivation**.