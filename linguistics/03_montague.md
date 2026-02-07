# Montague Grammar: English as a Formal Language

## Richard Montague

Richard Montague was a logician and philosopher who, in a series of papers in the early 1970s, showed how to give natural language the same rigorous semantic treatment as formal logic.

His famous declaration: "I reject the contention that an important theoretical difference exists between formal and natural languages."

## The Program

Montague's goal was to specify:
1. The syntax of English (or a fragment of it)
2. The semantics of English (truth conditions)
3. A systematic mapping from syntax to semantics

This was revolutionary because linguists had assumed natural language was too messy for formal treatment.

## Key Technical Ideas

### Type Theory

Every expression has a semantic type:

- **e**: Entities (individuals)
- **t**: Truth values (propositions)
- **e→t**: Properties (predicates of individuals)
- **(e→t)→t**: Quantifiers (functions from properties to truth values)
- **s→t**: Propositions (intensions)

Examples:
- "John" : e
- "runs" : e→t
- "every" : (e→t)→((e→t)→t)

### Lambda Calculus

Composition via lambda abstraction and application:

- "loves Mary" = λx.love(x, mary) : e→t
- "John loves Mary" = (λx.love(x, mary))(john) = love(john, mary) : t

### Intensional Logic

Montague added **intensions** — functions from possible worlds to extensions:

- The **extension** of "president" in our world is Biden
- The **intension** of "president" is the function that maps each world to whoever is president in that world

This handles:
- "John believes that the president is wise" (John might not know who the president is)
- "John wants to find a unicorn" (The unicorn might not exist)

### The PTQ Fragment

Montague's most famous paper: "The Proper Treatment of Quantification in Ordinary English" (PTQ)

He gave a complete formal grammar and semantics for a fragment of English including:
- Proper names and common nouns
- Transitive and intransitive verbs
- Quantifiers (every, some, a)
- Pronouns and anaphora
- Intensional verbs (believe, seek)

## Example Derivation

"Every man runs"

Syntax: