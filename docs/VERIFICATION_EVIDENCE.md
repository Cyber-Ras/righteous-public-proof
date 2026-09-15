# Verification Evidence

This document summarizes selected verification performed during Righteous development.

## Context Management v1

Context Management v1 replaced fixed-count recent-history selection with budget-aware context admission.

Verification covered:

- priority-aware admission of required and optional context;
- recent conversation selection within the configured input budget;
- preservation of chronological prompt order while prioritizing newer conversation during selection;
- cases where some older history must be omitted;
- cases where no optional history can be admitted;
- blocking reasoning-model inference when the required input alone exceeds the implemented guard;
- preservation of the complete user input when that guard blocks inference.

The result is a context pipeline that distinguishes between information Righteous has stored and information that should enter the model's active context for a particular turn.

## Earlier Conversation Retrieval

Development then extended beyond recent conversation into retrieval of older history.

### Recent / Earlier Ownership

The first step established a clear boundary between:

- the recent conversation region already handled by Context Management; and
- older conversation units eligible for Earlier Conversation retrieval.

This prevents the same conversation material from being owned by both retrieval paths.

### Semantic Ranking

Local semantic ranking was then implemented over genuine Earlier-searchable conversation units using embeddings and cosine similarity.

A controlled retrieval evaluation produced:

| Metric | Result |
|---|---:|
| Top-1 | 5 / 7 |
| Top-3 | 7 / 7 |
| Top-5 | 7 / 7 |
| Mean Reciprocal Rank | 0.857 |

The evaluation demonstrated that semantic retrieval could surface relevant older conversation material, including cases where simple word matching was insufficient.

EmbeddingGemma became the implemented retrieval model for this work.

Semantic similarity is used as a relevance signal, not as proof that retrieved information is true or authoritative.

## Persistent Embedding Cache

Persistent caching was added so compatible candidate embeddings could be reused across retrieval runs.

In a controlled 100-candidate regression:

| Measurement | Cold run | Warm run |
|---|---:|---:|
| Candidate embeddings generated | 100 | 0 |
| Cache misses | 100 | 0 |
| Cache hits | 0 | 100 |
| Candidate cache rows | 100 | 100 |

The warm run reused all compatible candidate vectors.

Ranking order remained equivalent between the cold and warm runs, with score differences remaining within the tested tolerance.

Additional regression coverage included:

- valid cache hits;
- cache misses;
- malformed vector handling;
- duplicate prevention;
- compatibility checks;
- selective invalidation;
- preservation and reuse of unchanged compatible records.

The embedding cache remains separate from canonical conversation history and can be rebuilt from that source data.

## Controlled Synthetic Observation

A separate sanitized observation used fictional data and isolated scratch storage to demonstrate four public-safe behaviors.

| Observation | Result |
|---|---|
| Oversized required input blocked reasoning-model inference | **PASS** |
| Rejected synthetic user input was preserved exactly | **PASS** |
| Cold candidate-vector misses became warm cache hits with identical ranking and scores | **PASS** |
| A malformed matching cache record produced explicit validation failure without silent reuse or replacement | **PASS** |

The observation recorded **0 reasoning-model inference calls** during the oversized-input test.

The complete sanitized record is available in:

[`../evidence/OBSERVATION_REPORT.md`](../evidence/OBSERVATION_REPORT.md)

## Current Retrieval Boundary

The Earlier Conversation retrieval pipeline can currently identify and rank relevant older conversation units and reuse compatible cached embeddings.

Those ranked Earlier Conversation results are **not currently inserted into ordinary response-generation context**.

That boundary is important to the current engineering state: retrieval has been implemented and verified independently before integration into ordinary model-facing conversation.