# The NLP Pipeline

## Overview

The NLP pipeline transforms raw text into structured representations suitable for logical reasoning. It draws on decades of research in computational linguistics.

## Historical Context

### The Statistical Revolution (1990s)

Before the 1990s, NLP was mostly rule-based. Then statistical methods took over:

- **POS tagging**: HMMs, then CRFs
- **Parsing**: PCFGs, then discriminative models
- **NER**: Sequence labeling with learned features

### The Neural Revolution (2010s)

Deep learning transformed NLP:

- **Word embeddings**: Word2Vec, GloVe
- **Sequence models**: RNNs, LSTMs
- **Transformers**: BERT, GPT

### Why Pipelines Still Matter

Despite end-to-end learning, pipelines remain useful:

- Interpretability: See what each stage produces
- Modularity: Improve components independently
- Error analysis: Know where things go wrong
- Built cumulative progress over decades

## The Classic Pipeline

### Stage 1: Tokenization

Split text into tokens (words, punctuation):

    Input: "John's dog didn't bark."
    Output: ["John", "'s", "dog", "did", "n't", "bark", "."]

Challenges:
- Contractions: "didn't" → "did" + "n't"
- Clitics: "John's" → "John" + "'s"
- Multi-word expressions: "New York" — one or two tokens?

### Stage 2: Part-of-Speech Tagging

Label each token with its grammatical category:

    John/NNP 's/POS dog/NN did/VBD n't/RB bark/VB ./.

Standard tagset: Penn Treebank (45 tags).

### Stage 3: Lemmatization

Reduce words to base form:

    running → run
    better → good
    was → be

Useful for predicate normalization.

### Stage 4: Dependency Parsing

Identify grammatical relations:

    "John loves Mary"
    
    nsubj(loves, John)  # John is subject of loves
    dobj(loves, Mary)   # Mary is object of loves
    root(ROOT, loves)   # loves is the root

See `06_dependency_parsing.md` for details.

### Stage 5: Semantic Role Labeling

Identify predicate-argument structure:

    "John gave Mary a book"
    
    give.01
      ARG0: John (giver)
      ARG1: a book (thing given)
      ARG2: Mary (recipient)

See `07_semantic_role_labeling.md` for details.

### Stage 6: Coreference Resolution

Link mentions to entities:

    "John went home. He ate dinner."
    
    John = He (same entity)

See `08_coreference.md` for details.

### Stage 7: Word Sense Disambiguation

Determine which sense of a word:

    "I went to the bank"
    
    bank = financial institution (not river bank)

See `09_word_sense_disambiguation.md` for details.

## Modern Tools

### spaCy

Fast, industrial-strength NLP:

    import spacy
    nlp = spacy.load("en_core_web_lg")
    doc = nlp("John loves Mary")
    
    for token in doc:
        print(token.text, token.dep_, token.head)

### Stanza (Stanford NLP)

Research-oriented, multilingual:

    import stanza
    nlp = stanza.Pipeline('en')
    doc = nlp("John loves Mary")

### AllenNLP

Deep learning models for NLP:

    from allennlp.predictors import Predictor
    predictor = Predictor.from_path("srl-model")
    result = predictor.predict("John loves Mary")

## Pipeline Integration

Our system combines these stages:

    Text
     │
     ├─→ spaCy: tokenization, POS, dependencies
     ├─→ AllenNLP: SRL
     ├─→ Custom: coreference, WSD
     │
     ▼
    Structured representation
     │
     ├─→ Entity extraction
     ├─→ Predicate mapping
     ├─→ Role alignment
     │
     ▼
    Logical form

## Error Propagation

Pipeline errors compound:

- Tokenization error → POS error → Parse error → SRL error
- Each stage depends on previous stages

Mitigation:
- Use robust models
- Joint inference where possible
- Error-tolerant downstream processing