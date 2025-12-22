# Introduction

## The Problem with LLMs

Large language models have two core issues:

1. **Hallucinations** — Generating text not supported by training data
2. **Inconsistent reasoning** — No coherent world model

These are related. A system that reasons consistently cannot hallucinate.

## The Logical Random Field

The LRF unifies logical and probabilistic reasoning:

- **Generative** — Compresses and generates data
- **Consistent** — Maintains P(x) + P(not x) = 1
- **Explainable** — Inference traces through causal structure
- **Efficient** — Belief propagation in O(N * 2^n)

## This Project

A pipeline that:

1. Parses natural language to logical forms
2. Grounds against a knowledge base
3. Builds a factor graph
4. Runs belief propagation to answer queries