# The Vibe Bridge: Semi-Automatic Construction

## Vibe Coding and Its Descendants

In February 2025, Andrej Karpathy coined the term "vibe coding" in a widely-shared post:

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists. [...] I just see stuff, say stuff, run stuff, and copy paste stuff, and it mostly works."
>
> — Andrej Karpathy, February 2, 2025

The term captured something real about the emerging practice of LLM-assisted software development. Developers across the industry reported similar experiences: 10x productivity gains, entire projects built in hours instead of weeks, codebases growing beyond the developer's direct comprehension but continuing to function. The pattern was consistent — the human provides direction and judgment, the LLM provides implementation, and rapid iteration substitutes for careful upfront design.

By mid-2025, "vibe X" had become a productive template. Researchers spoke of "vibe science" — using LLMs to rapidly prototype experiments, generate hypotheses, and iterate on research directions. The pattern was always the same: exponential speedup in the mechanical parts of the work, with the human retaining strategic direction and quality judgment.

## Our Bridge Stage

The system described in this paper was built using exactly this pattern, applied across three dimensions:

**Vibe Coding.** The software infrastructure — the CLI tools, the test harnesses, the development platform, the coverage verification system — was built in collaboration with LLMs. The codebase spans multiple repositories and thousands of lines of code. The human author directed the architecture and made design decisions; the LLM generated implementations, debugged issues, and proposed solutions. Earlier iterations of the codebase were developed with ChatGPT (OpenAI); the current version and this paper were developed primarily with Claude (Anthropic). Both LLMs reviewed drafts of this paper and contributed to its argumentation.

**Vibe Science.** The research methodology itself followed the vibe pattern. Coverage tests were designed through rapid iteration — propose a linguistic phenomenon, generate a test case, run it through the system, observe the failure mode, fix the grammar or the inference engine, repeat. Hypotheses about grammar rule design, factor graph topology, and belief propagation behavior were tested in minutes rather than days. The 44 inference tests and 12 syntax tests that constitute our experimental evaluation were developed through dozens of such iteration cycles.

**Vibe Annotation.** Most significantly for our thesis, the formal linguistic annotations — grammar rules, lexicon entries, coverage test gold standards — were produced through LLM-assisted authoring. The human author did not hand-write grammar rules in the traditional sense. Instead, the process was: describe a linguistic phenomenon to the LLM, receive a candidate grammar rule and test case, evaluate it against the system, refine through dialogue. This is a new instantiation of the vibe pattern — "vibe annotation" — where the LLM serves as the linguistic annotator and the human serves as the quality gate.

## The Bridge Is Temporary

The critical point about this bridge stage is that it is explicitly temporary. Each of the three vibe dimensions is automatable:

- **Vibe coding** converges to autonomous software agents — systems that can maintain and extend their own codebases without human direction. This is an active area of research and commercial development.

- **Vibe science** converges to autonomous research agents — systems that can formulate hypotheses, design experiments, and interpret results. Early versions of this capability already exist.

- **Vibe annotation** converges to the steady state described in the previous section — the LLM directly producing formal logical representations from text, with the QBBN providing verification. This is the specific contribution of our work: demonstrating that the formal representation exists, that inference over it is sound, and that LLM-assisted construction of it is feasible.

The semi-automatic bridge is how we get from "this was impossible" (pre-LLM) to "this runs autonomously" (steady state). The vibe pattern — human direction plus LLM execution plus rapid iteration — is the mechanism that makes the crossing possible. Without it, the formal approach would remain stuck at the annotation bottleneck that Sutton correctly identified as fatal. With it, the formal approach becomes not just viable but — for the first time in its history — scalable.

## Reconciling with the Bitter Lesson

Our position is not that Sutton was wrong. Our position is that LLMs are the mechanism by which the bitter lesson's prescription — scale with compute — finally becomes applicable to formal semantics.

Before LLMs, formal semantics could not scale with compute because the bottleneck was human annotation, which scales with human effort, not computation. After LLMs, the annotation is itself a computational process — one that scales with model size, training data, and inference budget. The formal representation remains hand-designed in its structure (predicates, roles, types, Horn clauses, factor graphs), but its *instantiation* — the specific grammar rules, lexicon entries, and logical forms for any given text — is produced by computation.

This is precisely the pattern Sutton celebrates in his examples. The rules of chess are hand-designed, but the search that plays chess at superhuman level is computational. The architecture of AlphaGo is hand-designed, but the self-play that produces superhuman Go strategy is computational. Similarly, the structure of our logical language and QBBN inference engine is hand-designed, but the annotation that maps natural language to logical forms is — increasingly — computational.

The bitter lesson was right. We are applying it.