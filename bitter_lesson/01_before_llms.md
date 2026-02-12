# Before LLMs: Why the Bitter Lesson Was Right

## The Promise of Formal Semantics

The intellectual tradition behind our work is deep and well-established. Montague (1973) showed that natural language could be given a fully formal semantics. Steedman's Combinatory Categorial Grammar provided compositional syntax-semantics interfaces. Head-Driven Phrase Structure Grammar, Definite Clause Grammars, and synchronous grammars all offered principled ways to map language to logic. The representations these systems produced were powerful: you could do genuine inference over them, check consistency, derive entailments, and provide formal guarantees about reasoning.

The problem was never the representation. It was the annotation.

## The Annotation Bottleneck

Every one of these systems required enormous human effort to construct. A grammar for even a fragment of English might require thousands of rules, each hand-written by a linguist who understood both the syntactic theory and the target logical formalism. Lexicons needed to be manually curated — every word sense, every argument structure, every selectional restriction specified by hand. Test suites required expert annotation of gold-standard logical forms.

The Penn Treebank, one of the most influential resources in NLP history, took years to construct and covered only a fraction of English syntax, with no formal semantics at all. FrameNet, PropBank, and the Abstract Meaning Representation (AMR) project all attempted to scale semantic annotation, and all hit the same wall: human annotators are slow, expensive, inconsistent, and finite.

This is the bottleneck Sutton's bitter lesson describes. Not a bottleneck of ideas — the ideas were good — but a bottleneck of construction. The formal approach required human knowledge not just in design but in every single instance of application. Every new sentence required a human to verify the parse. Every new word required a human to write the lexicon entry. The approach scaled linearly with human effort, which is to say it did not scale at all.

## The Statistical Revolution

The statistical revolution in NLP — from HMMs in the 1980s through CRFs and SVMs in the 2000s to deep learning after 2012 — succeeded precisely because it eliminated the annotation bottleneck for the *processing* stage. You still needed labeled data, but a POS tagger trained on the Penn Treebank could process unlimited new text. A neural parser trained on dependency annotations could parse any sentence. The human cost was amortized over the training set; at inference time, the cost was pure computation.

The formal semantics community watched this happen and largely could not compete. Statistical parsers were less principled but vastly more practical. Neural models produced representations that were opaque but useful. The bitter lesson played out exactly as Sutton described: methods that leveraged computation defeated methods that leveraged human knowledge.

## What Was Lost

But something was lost in the transition. Statistical and neural methods produce representations that are powerful for downstream tasks but opaque to inspection. A BERT embedding captures something about meaning, but you cannot ask it "does this sentence entail that one?" and get a formal proof. An LLM can answer questions with impressive accuracy, but it cannot show its reasoning in a form that can be independently verified. The guarantees that formal semantics offered — soundness, completeness, transparency — were traded for scalability.

The question this paper asks is whether that trade was permanent, or whether it was an artifact of a specific historical bottleneck that no longer exists.