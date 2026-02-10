# Head-Driven Phrase Structure Grammar

## Origins

HPSG was developed by Carl Pollard and Ivan Sag (1987, 1994) as a constraint-based grammar formalism. It replaced the transformational rules of Chomsky's Government and Binding theory with typed feature structures that unify during parsing.

## Typed Feature Structures

The central object in HPSG is the typed feature structure — a bundle of attribute-value pairs with a type label:

    word
    [ PHON    < "loves" >
      SYNSEM  [ LOCAL [ CAT  [ HEAD   verb
                                SUBCAT < NP, NP > ]
                         CONT [ RELN   love
                                LOVER  1
                                LOVED  2 ] ] ] ]

This single structure encodes:

- Phonology: the word is "loves"
- Syntax: it is a verb that subcategorizes for two NPs
- Semantics: it denotes the love relation with two participants

There is no separate level of syntactic representation and semantic interpretation. They are features of the same object.

## Unification

Parsing in HPSG works by unification. When two feature structures combine, their compatible features merge and incompatible features cause failure:

    [ CAT  verb          [ CAT  verb
      SUBJ [ CASE nom ] ]  SUBJ [ NUM  sg ] ]

    unifies to:

    [ CAT  verb
      SUBJ [ CASE nom
             NUM  sg ] ]

This is how agreement works, how subcategorization is checked, and how semantic roles get filled — all through the same mechanism.

## The Type Hierarchy

HPSG organizes its types into an inheritance hierarchy:

    sign
    ├── word
    └── phrase
        ├── headed-phrase
        │   ├── head-subject
        │   ├── head-complement
        │   └── head-modifier
        └── non-headed-phrase

Each type inherits constraints from its parents. A `head-complement` phrase inherits that it has a head daughter and a complement daughter, and that the head's SUBCAT list is consumed by the complement.

## The Lexicon Does the Work

A key principle of HPSG: most grammatical information lives in the lexicon, not in phrase structure rules. There are very few rules — perhaps a half dozen — and they are very general:

- Head-Complement Rule: a head combines with its complements
- Head-Subject Rule: a head combines with its subject
- Head-Modifier Rule: a head combines with a modifier

The specifics — what complements a verb takes, what case it assigns, what semantic roles it fills — all live in the lexical entry. The rules just say how to combine.

## Relevance

HPSG's insight that grammatical information belongs in the lexicon rather than in the rules is directly reflected in Typed Slot Grammar. Our lexicon entries carry semantic role signatures that determine where words can appear. Our grammar rules are few and general.

The typed feature structures of HPSG are a more powerful version of our role signatures. Where we have `{theme: e, reference: e}`, HPSG has nested feature structures with inheritance and unification. Our approach is a deliberate simplification — flat role signatures instead of nested features — that trades expressiveness for transparency.

HPSG also shares with our approach the principle that syntax and semantics are not separate modules. The CONTENT feature is part of the same structure as the CATEGORY feature. Parsing fills in both simultaneously.

## Key References

- Pollard, C., & Sag, I. (1987). *Information-Based Syntax and Semantics, Vol. 1*
- Pollard, C., & Sag, I. (1994). *Head-Driven Phrase Structure Grammar*
- Copestake, A. (2002). *Implementing Typed Feature Structure Grammars*
- Müller, S. (2021). *Grammatical Theory: From Transformational Grammar to Constraint-Based Approaches*