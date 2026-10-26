---
title: LLMs
permalink: /llm/
---

# Large Language Models

Notes on building and evaluating applications powered by language models.

## Guides

- [Neural Network Architectures: From Perceptrons to Transformers]({{ '/llm/neural-network-architectures/' | relative_url }})
  ([简体中文]({{ '/llm/zh-cn/neural-network-architectures/' | relative_url }})) — a visual,
  chronological guide to the problems that shaped common neural network families.

## Topics to explore

- Prompt design, few-shot examples, and structured outputs.
- Embeddings and retrieval-augmented generation (RAG).
- Fine-tuning versus retrieval.
- Evaluation of correctness, groundedness, latency, and cost.

## Example: a retrieval workflow

1. Split source documents into meaningful chunks.
2. Embed and index the chunks with their source metadata.
3. Retrieve relevant passages for a question.
4. Ask the model to answer using the retrieved context and cite its sources.
5. Check the answer against a small, curated evaluation set.

Treat retrieved text as untrusted input, and keep credentials and private data out of prompts.

[Back to home]({{ '/' | relative_url }})
