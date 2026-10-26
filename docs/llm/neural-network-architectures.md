---
title: "Neural Network Architectures: From Perceptrons to Transformers"
permalink: /llm/neural-network-architectures/
---

# Neural Network Architectures: From Perceptrons to Transformers

[简体中文]({{ '/llm/zh-cn/neural-network-architectures/' | relative_url }})

Why are there so many neural network architectures? Each major design appeared
because an earlier design struggled with a particular kind of structure: pixels
have neighborhoods, sentences have order, graphs have links, and generation
requires more than classification.

This guide follows the major families in roughly chronological order. The dates
mark influential milestones, not single moments of invention. Research ideas
usually developed over years and often became practical much later. Families are
ordered by their earliest roots; later descendants remain with their parent
family, so some date ranges overlap.

![Timeline of major neural network architecture milestones]({{ '/assets/llm/neural-network-timeline.svg' | relative_url }})

## Read the map before the timeline

An Artificial Neural Network (ANN) is a collection of simple computational units
connected by learned weights. An **architecture** specifies how those units are
connected and how information moves between them.

Most architectures in this guide emphasize one of four patterns:

![Four recurring neural network connection patterns]({{ '/assets/llm/architecture-patterns.svg' | relative_url }})

| Pattern | What it assumes | Representative families |
| --- | --- | --- |
| Dense connections | Every input may interact with every output. | Feedforward networks |
| Local connections | Nearby values form meaningful patterns. | Convolutional networks |
| Recurrent connections | Earlier states influence later states. | Recurrent networks |
| Selective connections | The model should choose which tokens, nodes, or experts matter. | Transformers, graph networks, mixtures of experts |

The families overlap. An autoencoder describes a training shape, for example,
while its encoder may itself be convolutional, recurrent, or Transformer-based.

## 1957–1986: perceptrons and feedforward networks

### The problem they addressed

Early researchers wanted a machine that could learn a decision rule from
examples instead of following a complete hand-written program. The perceptron,
introduced by Frank Rosenblatt in 1957 and published in 1958, learned a linear
boundary between two classes.

A single perceptron cannot solve problems that require several stages of
reasoning. Stacking units into a Feedforward Neural Network (FNN) made richer
functions possible. The popularization of backpropagation in 1986 supplied an
effective way to adjust weights across several layers.

```text
input features       hidden representations       prediction
[x1, x2, x3]  ───→  [layer 1] ───→ [layer 2] ───→ class or value
```

### Defining characteristics

- Information moves in one direction, from input to output.
- A Multilayer Perceptron (MLP) usually connects every unit in one layer to every
  unit in the next layer.
- Hidden layers and nonlinear activation functions let the network represent
  curved and discontinuous decision boundaries.
- The architecture makes few assumptions about how the input is organized.

### Where it is useful

Use an MLP for tabular classification, regression, compact learned features, or
as the prediction head attached to a larger model. It remains an important
baseline because it is simple and broadly applicable.

### Main limitation

Fully connected layers ignore known structure. Flattening an image destroys the
idea that neighboring pixels belong together, and processing each time step
independently discards sequence order.

## 1980–1998: convolutional networks learn local patterns

### The problem they addressed

Images are large, and the same visual feature can appear anywhere. Connecting
every pixel to every hidden unit wastes parameters and does not naturally reuse
a detector for an edge or corner at different positions.

The neocognitron in 1980 introduced an influential hierarchical, shift-tolerant
vision design. Trainable Convolutional Neural Networks (CNNs), including LeNet in
the late 1980s and 1990s, turned that principle into practical recognition
systems.

```text
image → edge maps → shapes and parts → object representation → prediction
        small local filters are reused across the entire image
```

### Defining characteristics

- A small filter slides across the input and shares its weights at every position.
- Early layers detect local patterns; deeper layers combine them into larger ones.
- Pooling or strided operations can reduce resolution while retaining important
  features.
- Local connectivity gives CNNs a strong prior for spatial data.

### Where it is useful

CNNs are effective for image classification, object detection, segmentation,
medical imaging, audio spectrograms, and one-dimensional sensor sequences.
Important descendants include AlexNet, the Visual Geometry Group network (VGG),
Residual Network (ResNet), DenseNet, EfficientNet, and U-Net.

### Main limitation

A CNN is naturally local. Very deep stacks, larger kernels, or additional
mechanisms may be needed to connect distant regions. Its built-in assumption of
translation-friendly local patterns is not appropriate for every dataset.

## 1980s–1997: recurrent networks give sequences a memory

### The problem they addressed

A feedforward model sees a fixed input and produces a fixed output, but language,
speech, and time series unfold one step at a time. The meaning of the current
value often depends on what came before it.

A Recurrent Neural Network (RNN) reuses the same transition at each step and
passes a hidden state forward. Work on recurrent systems grew throughout the
1980s. The Long Short-Term Memory (LSTM) network, introduced in 1997, addressed
the difficulty of learning dependencies across many steps.

```text
                     ┌──────── hidden state ────────┐
                     │                               ▼
step 1: x1 → [RNN cell] → h1   step 2: x2 → [same cell] → h2 → ...
```

### Defining characteristics

- The hidden state acts as a compressed summary of earlier inputs.
- Parameters are shared across time, allowing variable-length sequences.
- LSTM uses gates to decide what to write, preserve, and reveal from memory.
- The Gated Recurrent Unit (GRU), introduced in 2014, offers a simpler gated
  alternative.

### Where it is useful

RNNs work for forecasting, sequence labeling, speech, handwriting, and smaller
language systems. They remain useful when data arrives as a stream, latency must
be low, or the model must maintain a compact state.

### Main limitation

Basic RNNs suffer from vanishing or exploding gradients. Gated variants improve
memory, but recurrent processing is sequential and therefore difficult to
parallelize over a long sequence.

## 1980s–2013: autoencoders learn a useful bottleneck

### The problem they addressed

Labels are expensive, but raw examples are abundant. Researchers needed a way to
learn useful representations directly from the input and to compress data without
manually defining every feature.

An autoencoder learns to copy its input through a restricted middle
representation. Work in the 1980s used backpropagation to learn such internal
codes. Later variants made the bottleneck sparse, robust to noise, or
probabilistic.

```text
input ──→ encoder ──→ compact code ──→ decoder ──→ reconstruction
  │                                                   │
  └──────────────── compare input and output ─────────┘
```

### Defining characteristics

- The encoder compresses the input; the decoder reconstructs it.
- A bottleneck, noise, or another constraint prevents trivial copying.
- The learned code can capture factors that explain common patterns in the data.
- A Variational Autoencoder (VAE), introduced in 2013, learns a smooth
  probabilistic latent space from which new examples can be sampled.

### Where it is useful

Autoencoders support dimensionality reduction, denoising, representation
learning, compression, anomaly detection, and generation. Convolutional
autoencoders are common for images; sequence autoencoders handle text or time
series.

### Main limitation

Low reconstruction error does not guarantee a useful or interpretable
representation. A powerful decoder may reconstruct unusual inputs too well,
which can weaken anomaly detection.

## 1991: mixtures of experts divide the work

### The problem they addressed

One model must otherwise use the same computation for every example, even when
the data contains very different subproblems. A handwriting model, for instance,
might benefit from specialists for distinct writing styles.

The adaptive Mixture of Experts (MoE) architecture introduced in 1991 trained a
gating network to assign examples to specialized expert networks.

```text
                   ┌→ expert A ─┐
input → router ────┼→ expert B ─┼→ weighted or selected output
                   └→ expert C ─┘
```

### Defining characteristics

- Several expert subnetworks learn different regions or types of input.
- A router assigns weights to experts or selects a small subset.
- Modern sparse MoE layers activate only a few experts for each token.
- Capacity can grow faster than the computation used for one example.

### Where it is useful

MoE is useful for large, diverse datasets, multitask systems, and very large
language or vision models. Modern examples place sparse expert layers inside a
Transformer.

### Main limitation

Routing is difficult to balance. Some experts can receive too much work while
others learn little, and distributed systems must move activations efficiently
between devices.

## 1993: Siamese networks learn what “similar” means

### The problem they addressed

Ordinary classifiers choose among classes seen during training. Verification
asks a different question: are these two examples from the same person or
category, perhaps one the model has never seen before?

A Siamese network, introduced for signature verification in 1993, sends two
inputs through identical subnetworks with shared weights and compares their
embeddings.

```text
input A → shared encoder → embedding A ─┐
                                        ├→ distance → same or different
input B → shared encoder → embedding B ─┘
```

### Defining characteristics

- Two or more branches use exactly the same parameters.
- Training pulls matching examples together and pushes nonmatching examples apart.
- The output is a similarity or distance rather than a fixed class identity.

### Where it is useful

Siamese networks support face and signature verification, image matching,
semantic search, duplicate detection, and few-shot learning. The broader idea is
now often called metric learning or contrastive learning.

### Main limitation

Performance depends heavily on how positive and negative pairs are sampled. An
embedding may learn shortcuts if training pairs do not represent real deployment
conditions.

## 2005–2016: graph networks follow relationships

### The problem they addressed

Pixels lie on a regular grid and words form a sequence, but molecules, roads,
social networks, and knowledge bases have irregular connections. Flattening a
graph discards the links that define its structure.

Early Graph Neural Network (GNN) formulations appeared in 2005 and were developed
further in 2009. Later message-passing methods and the Graph Convolutional Network
(GCN) popularized practical graph learning in the mid-2010s.

```text
A ─── B        each node receives messages from its neighbors
│   ╱ │        B combines information from A, C, and D
C ─── D        repeated layers spread information farther
```

### Defining characteristics

- Nodes update their representations by aggregating messages from neighbors.
- Shared update rules work on graphs of different sizes and shapes.
- Node, edge, and whole-graph features can all contribute to predictions.
- Graph Attention Networks (GATs) learn which neighbors deserve more weight.

### Where it is useful

GNNs are used for molecular property prediction, recommendation, fraud detection,
traffic forecasting, knowledge graphs, physical systems, and social-network
analysis.

### Main limitation

Deep message passing can make node representations too similar, a problem called
over-smoothing. Large graphs also create sampling, memory, and distributed
training challenges.

## 2014: adversarial networks turn generation into a game

### The problem they addressed

A useful generative model must produce realistic examples, but defining a direct
score for “looks like a real image” is hard. The Generative Adversarial Network
(GAN), introduced in 2014, learned that score through competition.

![Comparison of autoencoder, generative adversarial network, and diffusion workflows]({{ '/assets/llm/generative-models.svg' | relative_url }})

### Defining characteristics

- A generator transforms random input into synthetic examples.
- A discriminator learns to distinguish real examples from generated ones.
- The generator improves by learning to fool the discriminator.
- No explicit pixel-by-pixel reconstruction target is required.

### Where it is useful

GANs have been influential in photorealistic image synthesis, image-to-image
translation, super-resolution, style transfer, and synthetic training data.
Well-known descendants include Deep Convolutional GAN, CycleGAN, and StyleGAN.

### Main limitation

Two competing networks can be unstable to train. The generator may also produce
only a narrow range of outputs, known as mode collapse, even when individual
samples look convincing.

## 2015–2020: diffusion models learn to reverse noise

### The problem they addressed

GANs can generate sharp samples, but their adversarial training is fragile and
may miss parts of the data distribution. Diffusion models use a more stable
learning problem: recover clean data from progressively noisier versions.

The modern formulation appeared in 2015. Denoising Diffusion Probabilistic Models
(DDPMs) demonstrated high-quality image generation in 2020, and later latent
methods made the process more efficient.

```text
training:    clean example → add noise in small steps → almost pure noise
sampling:    random noise  → remove predicted noise repeatedly → new example
```

### Defining characteristics

- A fixed forward process gradually destroys structure with noise.
- A neural network learns the reverse denoising steps.
- Training is stable compared with balancing an adversarial pair.
- Guidance can steer sampling with text, labels, or other conditions.

### Where it is useful

Diffusion models are used for image, audio, video, three-dimensional content,
molecule, and time-series generation, as well as inpainting and controlled
editing.

### Main limitation

Generation traditionally requires many network evaluations. Faster samplers and
latent diffusion reduce the cost, but inference can still be slower than a
single-pass generator.

## 2017–present: Transformers connect distant information directly

### The problem they addressed

RNNs carry information through every intermediate step and process tokens
sequentially. Long-distance relationships are therefore difficult to preserve,
and training cannot fully exploit parallel hardware.

The Transformer, introduced in 2017, replaced recurrence with self-attention.
Every token can directly gather information from other relevant tokens in the
same sequence.

![Self-attention routes information between relevant tokens]({{ '/assets/llm/transformer-attention.svg' | relative_url }})

### Defining characteristics

- Self-attention computes a context-dependent mixture of other token
  representations.
- Positional information tells the model about order because attention alone is
  permutation-independent.
- Many tokens can be processed in parallel during training.
- Encoder-only models are strong at understanding, decoder-only models at
  generation, and encoder–decoder models at sequence transformation.

### Where it is useful

Transformers power translation, summarization, search, code generation, speech,
multimodal systems, and most modern Large Language Models (LLMs). The Vision
Transformer (ViT) adapted the design to image patches in 2020. Transformers also
serve time-series, biology, robotics, and recommendation tasks.

### Main limitation

Full attention compares every token with every other token, so memory and
computation grow quickly with sequence length. Large models also demand extensive
data, hardware, evaluation, and deployment safeguards.

## How the families fit together

Modern systems rarely use one family in isolation:

- A CNN or ViT can encode an image before a Transformer generates a caption.
- A GNN can encode a molecule before a diffusion model proposes a new structure.
- An autoencoder can compress images so diffusion operates in a smaller latent
  space.
- A Transformer can replace selected MLP blocks with sparse MoE layers.
- A Siamese training objective can teach CNN or Transformer encoders useful
  embeddings.

Architecture names answer **how information flows**. Training objectives answer
**what the model learns to do**. The two should not be confused: a Transformer
can be trained to classify, reconstruct, predict, compare, or generate.

## Quick selection guide

| Your data or goal | Reasonable starting point | Why |
| --- | --- | --- |
| Tabular classification or regression | MLP | Few structural assumptions and a simple baseline |
| Images with local patterns | CNN | Efficient local filters and weight sharing |
| Streaming or moderate-length sequences | LSTM or GRU | Compact state and incremental processing |
| Long sequences or multimodal context | Transformer | Direct long-range interactions and parallel training |
| Compression, denoising, or anomaly scoring | Autoencoder | Learns a constrained reconstruction |
| Pair matching or few-shot recognition | Siamese network | Learns a reusable similarity space |
| Molecules, networks, or linked entities | GNN | Preserves irregular relationships |
| Fast single-pass image generation | GAN | Direct sampling after training |
| High-quality controllable generation | Diffusion model | Stable denoising objective and flexible guidance |
| Extremely large conditional capacity | MoE | Activates specialists without using every parameter |

Start with the simplest architecture that represents the structure in the data.
A more fashionable model is not automatically a better model: compare accuracy,
data needs, latency, memory, interpretability, and maintenance cost on the actual
deployment task.

## Milestones and further reading

These papers are useful landmarks rather than an exhaustive history:

- Rosenblatt, [*The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain*](https://doi.org/10.1037/h0042519), 1958.
- Fukushima, [*Neocognitron: A Self-organizing Neural Network Model for a Mechanism of Pattern Recognition Unaffected by Shift in Position*](https://doi.org/10.1007/BF00344251), 1980.
- Rumelhart, Hinton, and Williams, [*Learning Representations by Back-propagating Errors*](https://doi.org/10.1038/323533a0), 1986.
- Jacobs et al., [*Adaptive Mixtures of Local Experts*](https://doi.org/10.1162/neco.1991.3.1.79), 1991.
- Bromley et al., [*Signature Verification Using a Siamese Time Delay Neural Network*](https://proceedings.neurips.cc/paper/1993/hash/288cc0ff022877bd3df94bc9360b9c5d-Abstract.html), 1993.
- Hochreiter and Schmidhuber, [*Long Short-Term Memory*](https://doi.org/10.1162/neco.1997.9.8.1735), 1997.
- LeCun et al., [*Gradient-Based Learning Applied to Document Recognition*](https://doi.org/10.1109/5.726791), 1998.
- Scarselli et al., [*The Graph Neural Network Model*](https://doi.org/10.1109/TNN.2008.2005605), 2009.
- Kingma and Welling, [*Auto-Encoding Variational Bayes*](https://arxiv.org/abs/1312.6114), 2013.
- Goodfellow et al., [*Generative Adversarial Nets*](https://papers.nips.cc/paper/5423-generative-adversarial-nets), 2014.
- Sohl-Dickstein et al., [*Deep Unsupervised Learning Using Nonequilibrium Thermodynamics*](https://proceedings.mlr.press/v37/sohl-dickstein15.html), 2015.
- Vaswani et al., [*Attention Is All You Need*](https://arxiv.org/abs/1706.03762), 2017.
- Ho, Jain, and Abbeel, [*Denoising Diffusion Probabilistic Models*](https://arxiv.org/abs/2006.11239), 2020.
- Dosovitskiy et al., [*An Image Is Worth 16x16 Words: Transformers for Image Recognition at Scale*](https://arxiv.org/abs/2010.11929), 2020.

[← Back to Large Language Models]({{ '/llm/' | relative_url }})



