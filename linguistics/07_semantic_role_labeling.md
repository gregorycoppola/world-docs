# Semantic Role Labeling

## Overview

Semantic Role Labeling (SRL) identifies the predicate-argument structure of a sentence — "who did what to whom, where, when, and how."

Given a sentence like "John gave Mary a book in the library," SRL produces:

    Predicate: give.01
    ARG0 (giver): John
    ARG1 (thing given): a book
    ARG2 (recipient): Mary
    ARGM-LOC (location): in the library

## Semantic Roles

### Core Arguments (ARG0-ARG5)

Numbered arguments are predicate-specific:

    give.01:
      ARG0: giver
      ARG1: thing given
      ARG2: entity given to
    
    break.01:
      ARG0: breaker
      ARG1: thing broken

### Thematic Roles

More general categories:

| Role | Description | Example |
|------|-------------|---------|
| Agent | Doer of action | "John broke the window" |
| Patient | Affected entity | "John broke the window" |
| Theme | Entity in motion/location | "John put the book on the shelf" |
| Experiencer | Entity experiencing | "John felt sad" |
| Instrument | Tool used | "John cut with a knife" |
| Location | Where | "John slept in the bed" |
| Goal | Destination | "John went to Paris" |
| Source | Origin | "John came from London" |

### Modifier Arguments (ARGM-*)

Non-core arguments:

    ARGM-TMP: temporal ("yesterday")
    ARGM-LOC: location ("in Paris")
    ARGM-MNR: manner ("quickly")
    ARGM-CAU: cause ("because of rain")
    ARGM-PRP: purpose ("to help")
    ARGM-DIR: direction ("northward")
    ARGM-NEG: negation ("not")

## PropBank and FrameNet

### PropBank

Annotates predicates with numbered arguments:

    Sentence: "John broke the window"
    
    break.01:
      ARG0: John
      ARG1: the window

Simple, consistent, widely used.

### FrameNet

Richer semantic frames:

    Frame: Cause_harm
    
    Agent: John
    Victim: Mary
    Body_part: arm
    Instrument: knife

More semantic detail, but sparser coverage.

## SRL Models

### Traditional

Feature-based classifiers with syntactic features:

1. Parse sentence
2. Identify predicates
3. For each predicate, classify each constituent

### Neural

End-to-end neural models:

- BiLSTM with CRF (He et al., 2017)
- BERT-based (Shi & Lin, 2019)

State-of-the-art: ~87% F1 on CoNLL-2005.

## Relevance to QBBN

SRL provides exactly what we need for logical form:

    SRL output:
      give.01(ARG0: John, ARG1: book, ARG2: Mary)
    
    Our logical form:
      give(agent: john, theme: book, recipient: mary)

The mapping is nearly direct:

| SRL | Our Roles |
|-----|-----------|
| ARG0 | agent |
| ARG1 | theme/patient |
| ARG2 | recipient/goal |
| ARGM-LOC | location |
| ARGM-TMP | time |

### Example Pipeline

    Input: "John gave Mary the book yesterday"
    
    SRL:
      give.01
        ARG0: John
        ARG1: the book
        ARG2: Mary
        ARGM-TMP: yesterday
    
    Logical form:
      give(agent: john, patient: book, recipient: mary, time: yesterday)

## Challenges

### Implicit Arguments

    "The window broke"
    
    ARG1: the window
    ARG0: (implicit — something broke it)

### Light Verbs

    "John took a walk"
    
    The semantic predicate is "walk", not "take".

### Nominalization

    "John's destruction of the city"
    
    destruction → destroy.01
    ARG0: John
    ARG1: the city

## Tools

**AllenNLP**: BERT-based SRL

    from allennlp.predictors import Predictor
    predictor = Predictor.from_path(
        "https://storage.googleapis.com/allennlp-public-models/bert-base-srl-2020.03.24.tar.gz"
    )
    result = predictor.predict("John gave Mary a book")

**SENNA**: Fast C-based SRL

**Illinois SRL**: Feature-rich traditional system