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

    Input → Hidden₁ → Hidden₂ → ... → Output

Each layer applies: h = σ(Wx + b)

**Universal Approximation Theorem**: A network with one hidden layer can approximate any continuous function (given enough neurons).

## Activation Functions

**Sigmoid**: σ(x) = 1/(1 + e⁻ˣ)
- Range: (0, 1)
- Problem: Vanishing gradients

**Tanh**: tanh(x) = (eˣ - e⁻ˣ)/(eˣ + e⁻ˣ)
- Range: (-1, 1)
- Centered at zero

**ReLU**: ReLU(x) = max(0, x)
- Simple, effective
- Sparse activation
- Problem: "Dead" neurons

**GELU**: Smooth approximation to ReLU
- Used in transformers

## Backpropagation

The algorithm for computing gradients in neural networks.

**Forward pass**: Compute outputs layer by layer

**Backward pass**: Compute gradients layer by layer using chain rule

    ∂L/∂w = ∂L/∂y × ∂y/∂z × ∂z/∂w

Where z = wx + b, y = σ(z), L is the loss.

**Key insight**: Reuse intermediate computations (dynamic programming for gradients).

## Training

**Loss function**: Measures error between prediction and target
- Cross-entropy for classification
- MSE for regression

**Optimizer**: Updates weights to minimize loss
- SGD: w ← w - η∇L
- Adam: Adaptive learning rates + momentum

**Regularization**: Prevent overfitting
- Dropout: Randomly zero activations
- Weight decay: Penalize large weights
- Early stopping: Stop when validation loss increases

## Deep Learning Revolution

Starting ~2012, deep networks achieved breakthrough results:

**Computer Vision** (AlexNet, 2012):
- CNNs for image classification
- Hierarchical feature learning

**Speech Recognition** (2012+):
- RNNs and LSTMs for sequences
- End-to-end learning

**Natural Language Processing** (2018+):
- Transformers (see next document)
- Pre-training on large corpora

## Relationship to Graphical Models

Neural networks and graphical models are related:

| Aspect | Neural Networks | Graphical Models |
|--------|----------------|------------------|
| Structure | Learned representations | Explicit dependencies |
| Inference | Forward pass | Message passing |
| Learning | Backpropagation | EM, gradient descent |
| Uncertainty | Point estimates | Full distributions |

**Connections**:
- RBMs (Restricted Boltzmann Machines) are undirected graphical models trained with neural network techniques
- VAEs (Variational Autoencoders) combine neural networks with probabilistic inference
- Graph Neural Networks apply neural operations on graph structures

## Relevance to QBBN

QBBN is not a neural network, but:

1. **Weights can be learned**: The noisy-OR weights in Ψ_or factors can be trained by gradient descent, similar to neural network weights.

2. **Message passing ≈ forward/backward**: BP's π/λ messages are analogous to forward/backward passes.

3. **Complementary**: Neural networks (LLMs) can parse text to logical form, QBBN does reasoning.

The vision: **LLM for perception, QBBN for cognition**.

## Key References

- Rumelhart, D., Hinton, G., & Williams, R. (1986). "Learning Representations by Back-propagating Errors"
- LeCun, Y., Bengio, Y., & Hinton, G. (2015). "Deep Learning" (Nature review)
- Goodfellow, I., Bengio, Y., & Courville, A. (2016). *Deep Learning*