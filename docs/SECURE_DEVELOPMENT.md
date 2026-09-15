# Secure Development



Security decisions in Righteous have developed alongside the implementation rather than being added afterward as a separate layer.



The goal is to keep useful AI capability bounded by clear program behavior, controlled context, explicit data boundaries, and verifiable system state.



## Selected Engineering Controls



| Engineering concern | Current response |
|---|---|
| Unnecessary host information entering model context | Environment information is admitted selectively rather than exposing the full host environment by default |
| Historical responses replaying known environment identifiers | Prompt-facing historical context can be sanitized while the stored conversation record remains unchanged |
| Conversation history consuming too much context | Fixed-count history selection was replaced with budget-aware admission |
| Required input exceeding the implemented context guard | Python control flow blocks the reasoning-model call for that turn |
| Rejected input being lost | The complete user message remains preserved even when inference is blocked |
| Derived embeddings becoming confused with original conversation evidence | Embedding data is stored separately from canonical conversation history |
| Reusing incompatible or malformed cached vectors | Candidate vectors are validated before reuse |
| Deterministic controls depending only on model compliance | Important enforcement behavior is implemented in program control rather than relying only on prompts |



## Program Control vs. Model Reasoning



Righteous uses the language model for reasoning and language generation, but the model is not trusted to enforce every system rule by itself.



Where deterministic behavior matters, the surrounding program controls the decision.



This applies to areas such as:



- context admission;

- inference blocking;

- preservation of rejected input;

- environment-data exposure;

- embedding-cache validation;

- explicit memory-management behavior.



This distinction became an important engineering principle during development:



**model guidance ≠ trusted program control**



## Data Boundaries



Righteous separates several kinds of system state that could otherwise be treated as one thing.



Canonical conversation history remains distinct from:



- retrieved conversation history;

- active model context;

- derived embedding vectors;

- temporary ranking results.



Prompt-facing data can therefore be selected, filtered, or sanitized without rewriting the underlying stored conversation evidence.



The Stage 6 embedding cache is treated as derived and rebuildable data rather than as the authoritative source of conversation history.



## Context and Retrieval Safety



Persistent memory does not automatically mean unrestricted prompt access.



Context Management controls which available information is admitted for a given turn within the configured budget.



Earlier Conversation retrieval adds another distinction:



**semantic relevance does not establish truth or authority**



A historical conversation unit can be highly relevant to a new question while still being outdated, superseded, incomplete, or otherwise unsuitable as controlling information.



That distinction is being preserved as the retrieval system develops.



## Current Scope



The controls described here reflect the current implemented Righteous system.



They are intentionally bounded to the behavior that has been implemented and tested rather than presented as general guarantees about every future Righteous capability.
