# Sanitized Observation Report

## Observation Identity

- **Date:** September 14, 2026
- **Test label:** `RPP-STAGE6-SYNTH-2026-09-14-A`
- **Test type:** Controlled synthetic observation
- **Embedding runtime:** Local Ollama with `embeddinggemma:latest`

## Purpose

This observation exercised four selected behaviors using fictional inputs and isolated scratch storage:

1. oversized required-input rejection;
2. preservation of rejected user input;
3. cold-to-warm embedding-cache reuse;
4. explicit handling of a malformed matching cache record.

No genuine Righteous conversation or intentional-memory content was used as fixture data.

## Observation 1 — Oversized Required Input

A synthetic oversized input was used to trigger the implemented input guard.

| Observation | Result |
|---|---:|
| Guard triggered | Yes |
| Reasoning-model inference calls | 0 |
| Ordinary Righteous responses created | 0 |

**Result: PASS**

## Observation 2 — Input Preservation

The same rejected synthetic input was checked after inference was blocked.

| Observation | Result |
|---|---:|
| Stored synthetic user rows | 1 |
| Exact content comparison | Match |
| Truncation observed | No |
| Discard observed | No |

**Result: PASS**

## Observation 3 — Cold / Warm Cache Behavior

A three-candidate synthetic retrieval domain was ranked twice using the same query and candidate set.

| Candidate-vector observation | Cold run | Warm run |
|---|---:|---:|
| Cache lookups | 3 | 3 |
| Cache misses | 3 | 0 |
| Cache hits | 0 | 3 |
| Candidate-vector generations | 3 | 0 |
| Cache insertions | 3 | 0 |
| Cache rows after run | 3 | 3 |

Ranking order and scores were identical between the cold and warm runs.

The query embedding remained transient and was generated once per run.

**Result: PASS**

## Observation 4 — Malformed Matching Cache Record

A deliberately malformed matching cache record was introduced in isolated scratch state.

| Observation | Result |
|---|---:|
| Matching-record lookups | 1 |
| Explicit validation failures | 1 |
| Candidate regenerations | 0 |
| Replacement insertions | 0 |
| Silent vector return | No |

Observed exception:

`RuntimeError: Embedding vector is not a list`

**Result: PASS**

## Summary

All four observations passed.

The test used:

- fictional input data;
- isolated scratch databases;
- temporary observation resources;
- the current implemented retrieval/cache behavior.

Canonical Righteous files and live databases were not modified during the observation.