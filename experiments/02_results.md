# Experimental Results

## Summary

Current test results: **40 / 44 passing (91%)**

    ════════════════════════════════════════════════════════════
      ✅ Pass: 40  ❌ Fail: 4  💥 Error: 0  ⊘ Skip: 0
    ════════════════════════════════════════════════════════════

## Passing Tests (40)

All major reasoning patterns work:

| Category | Tests | Status |
|----------|-------|--------|
| and_gates | 3/3 | ✅ |
| and_plus_depth | 2/2 | ✅ |
| causation | 2/2 | ✅ |
| conditionals | 2/2 | ✅ |
| contrapositive | 1/1 | ✅ |
| counting | 1/1 | ✅ |
| degree | 1/1 | ✅ |
| depth_chains | 3/3 | ✅ |
| disjunction | 1/2 | ⚠️ |
| hypothetical | 1/2 | ⚠️ |
| identity | 2/2 | ✅ |
| modality | 2/2 | ✅ |
| multiple_rules | 2/2 | ✅ |
| negation | 1/1 | ✅ |
| possessives | 2/2 | ✅ |
| quantifier_scope | 2/2 | ✅ |
| relative_clauses | 2/2 | ✅ |
| roles | 2/2 | ✅ |
| sets | 2/2 | ✅ |
| spatial | 2/2 | ✅ |
| symmetric | 2/2 | ✅ |
| time | 0/2 | ❌ |
| transitivity | 2/2 | ✅ |

## Failing Tests (4)

### disjunction/01_lonely_or_exciting

**Issue**: "sometimes" modal gives P ≈ 0.5, classified as "unknown" instead of "yes".

**Root cause**: The test expects "yes" but "sometimes" semantically means 50% probability. This is arguably correct behavior — "sometimes X" shouldn't be confidently "yes".

**Resolution options**:
1. Change expected answer to "unknown"
2. Lower threshold for "yes" from 0.9 to 0.5
3. Redefine "sometimes" to mean "at least possible"

### hypothetical/02_would_win

**Issue**: "likely" modal gives P ≈ 0.7, below 0.9 threshold.

**Root cause**: Same as above — soft modals give soft probabilities.

**Resolution options**: Same as above.

### time/01_trust_now

**Issue**: "usually" gives P ≈ 0.9, just below threshold.

**Root cause**: Threshold is > 0.9, and "usually" gives exactly 0.9.

**Resolution options**:
1. Change threshold to >= 0.9
2. Adjust "usually" weight slightly higher

### time/02_allies_now

**Issue**: Chained "usually" gives P ≈ 0.81 (0.9 × 0.9).

**Root cause**: Probabilistic chains compound — two 90% steps give 81%.

**Resolution options**:
1. Accept as correct behavior (chains reduce confidence)
2. Change expected answer to "unknown"
3. Use deterministic rules in test

## Analysis

### What Works

**Deterministic reasoning**: All tests with "always" rules pass.

**Forward chaining**: Causes propagate to effects correctly.

**Backward chaining**: Contrapositive reasoning works (the recent fix).

**Conjunction**: AND gates correctly require all premises.

**Negation**: Negation factors correctly link P and ¬P.

**Depth**: Chains of 3-4 inference steps work.

### What's Borderline

**Soft modals**: "usually", "likely", "sometimes" work correctly but threshold interpretation differs from test expectations.

**Chained soft rules**: Probability compounds, which may or may not match intuition.

### Classification

The 4 failures are all **threshold/semantics issues**, not inference bugs:
- The probabilities computed are mathematically correct
- The disagreement is about how to interpret soft quantifiers

## Recommendations

### Option A: Strict Interpretation

Keep current behavior. Soft quantifiers give soft answers.
- Change test expectations to match
- "sometimes X" → expected: unknown
- "usually X" chained → expected: unknown

### Option B: Pragmatic Interpretation

Adjust thresholds for practical use.
- "yes" if P > 0.5 (more likely than not)
- "no" if P < 0.5
- "unknown" only if P ≈ 0.5

### Option C: Confidence Intervals

Report uncertainty, not just point estimates.
- P = 0.7 ± 0.1 → "likely yes"
- P = 0.5 ± 0.3 → "uncertain"