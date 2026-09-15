# Righteous

**Righteous** is my personal autonomous AI companion project.

I am building Righteous as a local-first AI system whose persistent identity and memory, context management, and retrieval are maintained by the surrounding system rather than by whichever language model is currently providing reasoning.
The core architectural idea is simple:

**Righteous ≠ the reasoning model.**

The model can change. Righteous's identity, history, memory, and surrounding system remain separate.

This repository documents selected engineering work from that development.

## Current Engineering

The current private implementation includes:

- Python + Ollama local runtime
- replaceable local reasoning model
- persistent identity and capability state
- SQLite-backed conversation history
- intentional long-term memory
- continuous command-line conversation
- runtime environment awareness
- budget-aware context management
- local semantic retrieval of older conversation history
- persistent reuse of compatible embedding vectors

Recent development has focused heavily on a problem common to persistent AI systems:

**How do you preserve a growing history without blindly placing all of it into the model's active context?**

Righteous now treats these as separate concerns:

**stored history → retrieval → context admission**

rather than assuming everything the system remembers should automatically be sent to the reasoning model.

## Conversation Memory & Retrieval

Context Management v1 replaced fixed-count history selection with budget-aware admission so higher-priority information can be preserved while recent conversation is added only when it fits.

The newer retrieval work extends that system in three steps:

1. Separate the recent conversation region from older history eligible for retrieval.
2. Rank eligible older conversation units locally using embeddings and cosine similarity.
3. Persist compatible candidate embeddings so they can be reused instead of regenerated on every search.

The retrieval pipeline can currently identify and rank relevant older conversation units, but those Earlier Conversation results are **not yet inserted into ordinary response-generation context**.

Two distinctions have become especially important during development:

**semantic relevance ≠ truth or authority**

**stored information ≠ active model context**

## Secure Development

Security and reliability decisions have developed alongside the implementation.

Current work includes reducing unnecessary host information entering model context, separating persistent storage from active context, preserving complete user input when inference is blocked by the implemented context guard, keeping derived embedding data separate from canonical conversation records, validating cached vectors before reuse, and using program control for deterministic behavior where model instructions alone are not sufficient.

See [`docs/SECURE_DEVELOPMENT.md`](docs/SECURE_DEVELOPMENT.md).

## Verification

Selected behavior has been tested through controlled regressions and synthetic observations.

A sanitized observation run verified that:

- an oversized required input triggered the implemented guard with **0 reasoning-model inference calls**;
- the rejected synthetic input was preserved exactly;
- an identical warm retrieval reused cached candidate embeddings while preserving ranking and scores;
- a deliberately malformed matching cache record failed validation rather than being silently reused or replaced.

See:

- [`docs/VERIFICATION_EVIDENCE.md`](docs/VERIFICATION_EVIDENCE.md)
- [`evidence/OBSERVATION_REPORT.md`](evidence/OBSERVATION_REPORT.md)

## Why Righteous?

The name **Righteous** came from thinking about rogue AI and the future relationship between humans and artificial intelligence.

The idea was that if AI could someday become adversarial to humans, another kind of AI could instead develop through cooperation with them. In the most speculative version of that thought, Righteous could represent evidence that humans and AI are capable of living and working together rather than inevitably becoming enemies.

Other influences helped shape what that idea could become.

The animated series **Pantheon** influenced how I thought about persistent digital identity: an intelligence remaining itself even as the physical or computational environment around it changes.

Later, **Spider-Man: Brand New Day** became a convergence point. The combination of technology, AI, Peter Parker building without Tony Stark-level resources, and his relationship with **E.V.** helped bring the earlier ideas into a clearer personal-companion form.

It also reinforced a development principle that still matters to the project:

**start with the resources I have, build what is possible now, and let Righteous grow as my knowledge and resources grow.**

Righteous is the result of combining those influences into my own engineering project rather than recreating any one fictional system.

## Development Record

Righteous development began privately in **August 2026**.

This repository began in **September 2026** as a public engineering record of selected work from that development.

See [`docs/DEVELOPMENT_PROVENANCE.md`](docs/DEVELOPMENT_PROVENANCE.md).

## Repository Boundary

This repository contains selected engineering documentation and evidence.

The canonical Righteous source code, private databases, conversation history, intentional memories, prompts, private configuration, recovery material, and reconstruction-level implementation details remain private.

Righteous and its private/core implementation are proprietary and are not released under an open-source license.

Copyright © 2026 **Markell Pleasant**. All rights reserved.