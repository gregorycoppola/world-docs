# Neural Networks

## What is a Neural Network?

A neural network is a computational model inspired by biological neurons. It consists of layers of interconnected units (neurons) that transform inputs to outputs through learned weights.

Neural networks are the foundation of modern deep learning and the technology behind large language models.

## The Perceptron

The simplest neural network: a single neuron.

**Input**: x = (x₁, ..., xₙ)
**Weights**: w = (w₁, ..., wₙ)
**Bias**: b
**Output**: y = σ(w·x + b)

Where σ is an activation function (e.g., sigmoid, ReLU).

**Learning**: Adjust weights to minimize error on training data.

## Multi-Layer Networks

Stack layers of neurons: