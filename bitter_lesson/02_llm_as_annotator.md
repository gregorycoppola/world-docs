# The LLM as Annotator

## The Key Insight

The central insight of this work is not a new representation, a new inference algorithm, or a new grammar formalism. It is an observation about what large language models make possible: **LLMs can replace the human annotator in the construction of formal semantic systems.**

The annotation bottleneck that killed formal NLP was a bottleneck of human effort. Grammar rules had to be written by linguists. Lexicon entries had to be curated by hand. Coverage tests had to be authored by experts who understood both the natural language input and the target logical output. Every step required a person who could bridge the gap between informal language and formal logic.

Large language models can bridge that gap. An LLM can:

- Read a natural language sentence and produce a candidate logical form
- Given a lexicon format specification, generate new lexicon entries for unseen words
- Given a grammar rule template, propose new rules that handle uncovered syntactic patterns
- Given a set of existing coverage tests, generate new tests that exercise different phenomena
- Review and critique proposed logical representations for consistency and completeness

None of these capabilities are perfect. LLMs hallucinate, produce inconsistent annotations, and sometimes miss subtle linguistic distinctions. But they do not need to be perfect. They need to be *good enough to iterate on* — good enough that a human can review and correct their output faster than authoring it from scratch. And for the task of formal annotation, current LLMs clear that bar decisively.

## The Steady State Vision

The fully automated version of this system looks like:

1. Raw natural language text enters the pipeline
2. An LLM (or ensemble of LLMs) produces candidate logical forms — facts, rules, queries
3. The formal inference engine (QBBN) processes these logical forms, runs belief propagation, and produces answers with associated probabilities
4. The inference results are checked for consistency — do the derived beliefs contradict known facts? Do the probabilities fall in expected ranges?
5. Inconsistencies are fed back to the LLM for revision
6. The cycle repeats until convergence

In this steady state, no human is in the loop. The LLM is the generator — it produces formal representations from informal input. The QBBN is the verifier — it checks the representations for logical consistency and derives consequences. The combination gives you what neither component provides alone: the LLM's broad language understanding plus the formal system's guarantees of sound inference.

This is, we argue, fully aligned with the bitter lesson. The LLM scales with compute — more parameters, more training data, better annotation. The formal inference engine scales with compute — more grounding, deeper search, more precise beliefs. The human knowledge that was the bottleneck — the grammar rules, the lexicon entries, the annotation guidelines — is replaced by learned capabilities. What remains is the formal structure itself, which is not human knowledge but mathematical infrastructure, no different from the rules of chess or Go that Sutton's own examples take for granted.

## The Verification Gap

The reason the formal layer is necessary — the reason you cannot simply let the LLM answer questions directly — is the verification gap. An LLM that says "Socrates is mortal" may be correct, but you cannot distinguish a correct answer from a confident hallucination without formal machinery. The logical representation makes the reasoning transparent: here are the facts, here are the rules, here is the grounding, here is the factor graph, here is the belief propagation trace, here is the posterior probability. Every step is inspectable and verifiable.

This is not a theoretical concern. LLM hallucination is the central practical problem in deploying language models for high-stakes reasoning. Our claim is that the solution is not to make the LLM more reliable — though that helps — but to give it a formal target that makes its output *checkable*. The QBBN is a runtime for natural language reasoning, in the same way that a Python interpreter is a runtime for code. You do not trust code because the programmer is reliable; you trust it because you can run it and observe the results.