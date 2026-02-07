# Reasoning

## The Problem

LLMs struggle with multi-step logical reasoning. They can pattern-match to similar training examples but fail on novel combinations.

### Examples of Failure

**Syllogism with novel terms**:

    All glorps are fribbles.
    All fribbles are snazzles.
    Is a glorp a snazzle?
    
    LLM: Often fails or hedges

**Longer chains**:

    A implies B. B implies C. C implies D. D implies E.
    A is true. Is E true?
    
    LLM: Accuracy degrades with chain length

**Negation**:

    All birds fly. Tweety is a bird. Tweety does not fly.
    Is there a contradiction?
    
    LLM: Often misses the contradiction

### Why LLMs Fail

LLMs don't "reason" — they retrieve and interpolate:

1. **No explicit inference**: No modus ponens, no resolution
2. **Pattern matching**: Find similar training examples
3. **Soft degradation**: Confidence doesn't match correctness
4. **No contradiction detection**: Inconsistencies go unnoticed

## The QBBN Solution

QBBN performs actual logical inference via belief propagation on a factor graph.

### Forward Inference (π messages)

Information flows from premises to conclusions:

    Facts: man(socrates)
    Rule: man(x) -> mortal(x)
    
    Forward pass:
      π(man(socrates)) = 1.0 [evidence]
      π(group) = 1.0 [AND: premise true]
      π(mortal(socrates)) = 1.0 [OR: group true]
    
    Result: mortal(socrates) = True

### Backward Inference (λ messages)

Information flows from conclusions to premises (contrapositive):

    Facts: not mortal(zeus)
    Rule: man(x) -> mortal(x)
    
    Backward pass:
      λ(mortal(zeus)) = [1, 0] [evidence: must be false]
      λ(group) = [1, 0] [OR backward: group must be false]
      λ(man(zeus)) = [1, 0] [AND backward: premise must be false]
    
    Result: man(zeus) = False

### Chain Reasoning

QBBN handles arbitrary chain length:

    Rules:
      glorp(x) -> fribble(x)
      fribble(x) -> snazzle(x)
    
    Fact: glorp(thing)
    
    Iteration 1: fribble(thing) = 1.0
    Iteration 2: snazzle(thing) = 1.0
    
    Result: snazzle(thing) = True

Each BP iteration propagates one step. Chains of length N converge in N iterations.

### Contradiction Detection

Contradictions surface as P = 0 for both options:

    Facts: bird(tweety), flies(tweety), not flies(tweety)
    
    After propagation:
      P(flies(tweety)) = impossible (both 0 and 1 required)
    
    System detects: Contradiction in KB

## Comparison to LLM "Reasoning"

| Aspect | LLM | QBBN |
|--------|-----|------|
| Mechanism | Pattern matching | Belief propagation |
| Chain length | Degrades | Constant per step |
| Novel terms | Often fails | Works fine |
| Soundness | Not guaranteed | Guaranteed |
| Contradiction | Often missed | Detected |

## Example: Legal Reasoning

    KB:
      contract_signed(party: alice, party: bob)
      contract_signed(x, y) -> obligation(x, y)
      obligation(x, y) & not fulfilled(x, y) -> breach(x)
      breach(x) -> liable(x)
      not fulfilled(party: alice, party: bob)
    
    Query: liable(alice)?
    
    Inference chain:
      1. contract_signed(alice, bob) [fact]
      2. obligation(alice, bob) [from 1 + rule]
      3. not fulfilled(alice, bob) [fact]
      4. breach(alice) [from 2, 3 + rule]
      5. liable(alice) [from 4 + rule]
    
    Result: Yes, with full derivation

## Soft Reasoning

QBBN also handles uncertain inference:

    Rule: usually [x:e]: politician(x) -> dishonest(x)
    Fact: politician(jones)
    
    Result: P(dishonest(jones)) ≈ 0.9

The modal "usually" gives weight 2.3, which translates to ~90% probability via noisy-OR.

## Key Insight

Reasoning requires **explicit inference steps**, not pattern matching. QBBN provides sound inference via belief propagation, handling both certain and uncertain reasoning.