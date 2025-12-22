# Syntactic Parsing Pipeline

## Key-Value Calculus

Instead of positional arguments:

    wrote(Shakespeare, Macbeth)

We use key-value notation:

    (WROTE, {arg0: Shakespeare, arg1: Macbeth})

Matches dependency parse structure.

## Pipeline Stages

1. Tokenization
2. Dependency parsing
3. Semantic role labeling
4. Entity linking
5. Proposition construction

## Example

Input: "John sent a letter to Sally"

Output:

    (SEND, {subj: John, dobj: letter, iobj: Sally})