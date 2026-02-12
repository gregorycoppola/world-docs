# The Bitter Lesson and the LLM Opportunity

Rich Sutton's "The Bitter Lesson" (2019) is one of the most influential essays in modern AI research. Its central claim is simple and well-supported: hand-engineered knowledge representations lose to general methods that scale with computation. Chess, Go, speech recognition, computer vision — in every case, researchers who built in domain knowledge were eventually overtaken by researchers who found ways to leverage more compute through search and learning.

The natural language processing pipeline we describe in this paper — parsing text into logical forms, grounding Horn clauses, running belief propagation over factor graphs — sits squarely on the side Sutton warns against. It is a hand-engineered system. It encodes linguistic knowledge explicitly. It is exactly the kind of approach that, historically, has been swept away by statistical and neural methods.

We agree with Sutton's historical analysis. Pre-LLM, this approach was not viable at scale. The bottleneck was human annotation: every grammar rule, every lexicon entry, every coverage test required a trained linguist. The representation itself was sound — formal semantics gives you verifiable inference that no neural network can match — but the cost of producing that representation was fatal.

Our thesis is that large language models change this equation fundamentally. Not by replacing the formal layer, but by replacing the human annotator. The limiting factor was never the representation — it was the cost of constructing it. LLMs eliminate that cost. And in doing so, they make the formal approach not just viable but — for the first time — compatible with the bitter lesson's own prescription: scale with compute.

This section develops this argument in three parts:

1. **Before LLMs**: Why Sutton was right, and why hand-built NLP pipelines failed in practice despite being sound in principle.

2. **The LLM as Annotator**: How LLMs change the equation by replacing human annotation with machine annotation, and what the fully automated steady state looks like.

3. **The Vibe Bridge**: The semi-automatic bridge stage we are currently in, where "vibe coding," "vibe science," and "vibe annotation" — patterns identified by Karpathy and the broader AI community in early 2025 — are being used to rapidly construct the formal infrastructure that will eventually run autonomously.