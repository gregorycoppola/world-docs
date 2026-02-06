# Steedman and Combinatory Categorial Grammar

## Mark Steedman

Mark Steedman is a computational linguist who developed Combinatory Categorial Grammar (CCG), a formalism that elegantly unifies syntax and semantics while remaining computationally tractable.

His work bridges:
- Chomsky's generative grammar
- Montague's formal semantics
- Practical statistical parsing

## Combinatory Categorial Grammar (CCG)

### Categories

Every word has a category that encodes its argument structure:

- Noun: `N`
- Noun phrase: `NP`
- Sentence: `S`
- Intransitive verb: `S\NP` (needs NP on left to make S)
- Transitive verb: `(S\NP)/NP` (needs NP on right, then NP on left)
- Determiner: `NP/N` (needs N on right to make NP)

The slashes indicate directionality:
- `/` means "looking right"
- `\` means "looking left"

### Combinatory Rules

**Forward Application (>)**