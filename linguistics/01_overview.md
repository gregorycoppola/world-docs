# Linguistics Overview: The Two Interfaces

## The Central Problem

Natural language sits at the intersection of two worlds:

1. **Sound/Text** — The physical medium (acoustic signals, written characters)
2. **Meaning** — The logical content (propositions, truth conditions, inference)

How do these connect? This is the fundamental question of linguistics, and it's the question our system must answer to translate English into logical forms for reasoning.

## Chomsky: The Two Interfaces

In the Minimalist Program, Noam Chomsky proposed that language has two "interfaces":

- **PF (Phonetic Form)** — The interface with the sensorimotor system (how we produce and perceive language)
- **LF (Logical Form)** — The interface with the conceptual-intentional system (how we think and reason)

The grammar is a computational system that maps between these interfaces. A sentence like "John loves Mary" has:

- A PF representation: the sounds /dʒɒn lʌvz ˈmɛəri/ or the written characters
- An LF representation: something like `love(agent: john, patient: mary)`

The syntax is the bridge — it provides the structure that determines both how the sentence is pronounced and what it means.

## Montague: English as a Formal Language

Richard Montague famously declared: "I reject the contention that an important theoretical difference exists between formal and natural languages."

His program (Montague Grammar) showed that English could be given a precise formal semantics, just like logic or mathematics. Key ideas:

- **Compositionality**: The meaning of a whole is a function of the meanings of its parts
- **Type theory**: Words have semantic types (e, t, e→t, etc.)
- **Lambda calculus**: Composition via function application and abstraction

For example:
- "John" denotes an entity: `john : e`
- "runs" denotes a property: `runs : e → t`
- "John runs" is function application: `runs(john) : t`

Montague showed this works for quantifiers, intensional verbs, and other complex phenomena. The logical forms are fully compositional from the syntax.

## Steedman: Combinatory Categorial Grammar

Mark Steedman unified Chomsky's syntax with Montague's semantics through Combinatory Categorial Grammar (CCG).

Key ideas:

- **Categories encode argument structure**: A transitive verb has category `(S\NP)/NP` — it needs an NP on the right (object) and an NP on the left (subject) to form a sentence
- **Combinatory rules**: Function application, composition, type-raising
- **Transparent syntax-semantics interface**: Each syntactic rule has a corresponding semantic rule
- **Surface structure = logical structure**: No transformations or movement needed

CCG is particularly elegant because:
1. The syntax directly produces the logical form
2. It handles coordination, extraction, and other hard phenomena
3. It's computationally tractable (polynomial parsing)

Steedman also pioneered **statistical CCG parsing**, showing that these formal grammars could be learned from data and applied to real text.

## Our System's Approach

We inherit from all three traditions:

From **Chomsky**: The idea that syntax mediates between form and meaning

From **Montague**: The goal of precise logical semantics for natural language

From **Steedman**: The practical methods for parsing to logical form

Our pipeline: