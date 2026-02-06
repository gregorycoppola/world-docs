# Application 1: Preventing Hallucinations

## The Problem

LLMs hallucinate. They generate text that sounds plausible but is not grounded in any source. This is perhaps the most critical problem limiting LLM deployment in high-stakes applications.

Examples:
- Citing papers that don't exist
- Stating false facts with high confidence
- Inventing biographical details about real people
- Generating plausible but incorrect code

The root cause: LLMs have no mechanism to distinguish between what they know and what they're generating. Their "knowledge" is distributed across billions of weights with no explicit provenance.

## How QBBN Prevents Hallucinations

In QBBN, every belief has an explicit source. A proposition can only have high probability if:

1. **It's direct evidence**: A fact asserted in the knowledge base
2. **It's derived via inference**: The conclusion of rules whose premises are themselves grounded

There is no third option. The system cannot generate beliefs from nothing.

### Example

Consider the query: "Is Socrates mortal?"

In an LLM:
- The model might answer "yes" because it has seen similar patterns
- Or it might answer "no" if the context is unusual
- There's no way to know why it gave that answer

In QBBN: