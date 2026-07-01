# Engineering & LLM Glossary

> A working vocabulary for software engineering, distributed systems, product, and LLM/AI work — the language you're expected to be fluent in for Anthropic/OpenAI-style engineering work, and increasingly for any modern product team.

This is **general industry vocabulary** — words any engineering team uses, not BlocLabs-specific terms. If you're looking for FabricBloc's own product vocabulary (Creator, Client, Consumer, Workspace, Primitive, and so on), that lives in the platform repo's `GLOSSARY.md`, not here. This file is a reference, not a phase — skim it once, then come back whenever a term in a PR, a design doc, or a conversation is unfamiliar.

---

## Core Engineering Terms

| Term | Definition |
|---|---|
| **Deterministic** | Same input always produces the same output. |
| **Non-deterministic** | Same input may produce different outputs. LLMs are often non-deterministic unless tightly controlled. |
| **Idempotent** | Running the same operation multiple times has the same effect as running it once. |
| **Latency** | How long a request takes to complete. |
| **Throughput** | How many requests/jobs a system can handle per unit of time. |
| **Backpressure** | Slowing producers down when consumers cannot keep up. |
| **Fan-out** | One request triggers many downstream requests or tasks. |
| **Fan-in** | Many tasks/results are gathered back into one place. |
| **Retry** | Trying an operation again after failure. |
| **Exponential backoff** | Waiting longer between retries to avoid overwhelming a system. |
| **Timeout** | A maximum allowed wait time before aborting. |
| **Circuit breaker** | Temporarily stop calling a failing dependency. |
| **Rate limit** | A cap on how often something can be called. |
| **Queue** | A buffer of work to be processed asynchronously. |
| **Worker** | A process that consumes jobs from a queue. |
| **Dead-letter queue** | Where failed jobs go after too many retries. |
| **Race condition** | Bug caused by timing between concurrent operations. |
| **Lock** | Mechanism to prevent concurrent access to shared state. |
| **Cache** | Stored result reused to avoid recomputation or network calls. |
| **Cache invalidation** | Deciding when cached data is stale. |
| **Consistency** | Whether different parts of a system agree on current state. |
| **Eventually consistent** | Data may be temporarily out of sync but converges later. |

## Frontend/Product Terms

| Term | Definition |
|---|---|
| **Optimistic UI** | Update the UI before the server confirms success, then roll back if it fails. |
| **Pessimistic UI** | Wait for server confirmation before updating the UI. |
| **Loading state** | UI state while waiting for data. |
| **Empty state** | UI shown when there is no data yet. |
| **Error state** | UI shown when something fails. |
| **Debounce** | Delay action until input stops changing. |
| **Throttle** | Limit how often an action can run. |
| **Progressive disclosure** | Show simple controls first, reveal complexity when needed. |
| **Affordance** | A visual cue that suggests how something can be used. |
| **Human-in-the-loop** | A person reviews or approves part of an automated workflow. |

## API Terms

| Term | Definition |
|---|---|
| **Endpoint** | A URL or method exposed by an API. |
| **Request/response** | The input sent to an API and the output returned. |
| **Payload** | The data body sent in a request or response. |
| **Schema** | The expected shape of data. |
| **Contract** | The agreed behavior between caller and service. |
| **Breaking change** | A change that can break existing callers. |
| **Backward compatible** | New change does not break existing callers. |
| **Streaming** | Returning output incrementally instead of all at once. |
| **Webhook** | Server-to-server callback triggered by an event. |
| **Pagination** | Splitting large results into pages. |
| **Cursor** | Token used to fetch the next page of results. |

## LLM / AI Terms

| Term | Definition |
|---|---|
| **Prompt** | Input instructions given to a model. |
| **System prompt** | High-priority instruction that shapes model behavior. |
| **User prompt** | The direct request a caller sends the model — the `user` role in a chat-style API call, as opposed to the `system` prompt. |
| **Context window** | Maximum amount of text the model can consider at once. |
| **Token** | Unit of text processed by a model. |
| **Temperature** | Controls randomness. Lower is more predictable; higher is more varied. |
| **Top-p / nucleus sampling** | Another way to control randomness. |
| **Completion** | Model-generated output. |
| **Inference** | Running the model to produce an answer. |
| **Embedding** | Numeric representation of text used for search, similarity, or retrieval. |
| **Vector database** | Database optimized for storing/searching embeddings. |
| **RAG** | Retrieval-augmented generation — fetch relevant data, then give it to the model. |
| **Fine-tuning** | Training a model further on task-specific data. |
| **Evaluation / eval** | Test that measures model behavior or quality. |
| **Golden dataset** | Trusted set of examples used for evaluation. |
| **Hallucination** | Model produces plausible but false information. |
| **Grounding** | Tying model output to provided evidence or sources. |
| **Tool use** | Model calls external tools/APIs to do work. |
| **Agent** | Model-driven system that plans, uses tools, and acts over multiple steps. |
| **Guardrail** | Constraint or check that prevents unsafe/incorrect behavior. |
| **Alignment** | Making model behavior match human intent and safety goals. |
| **RLHF** | Reinforcement learning from human feedback. |
| **Constitutional AI** | Anthropic's training/behavior method — uses a written set of principles as feedback guidance instead of only human labels. |

## Reliability / Observability Terms

| Term | Definition |
|---|---|
| **SLO** | Service-level objective — the internal reliability target a team commits to. |
| **SLA** | Service-level agreement — the reliability promise made externally. |
| **SLI** | Service-level indicator — the metric used to measure reliability. |
| **Uptime** | Percent of time a service is available. |
| **Error budget** | Allowed amount of failure before reliability work takes priority over features. |
| **Logs** | Recorded events from a system. |
| **Metrics** | Numeric measurements over time. |
| **Traces** | A request's path across services. |
| **Alert** | Notification when something crosses a threshold. |
| **Incident** | Production issue requiring a response. |
| **Postmortem** | Review after an incident to learn and prevent recurrence. |
| **Root cause** | The underlying reason something failed. |
| **Blast radius** | Scope of impact from a failure. |
| **Rollback** | Revert to a previous known-good version. |

## Security / Safety Terms

| Term | Definition |
|---|---|
| **Authentication** | Proving who someone is. |
| **Authorization** | Deciding what they can access. |
| **Principle of least privilege** | Give only the minimum permissions needed. |
| **PII** | Personally identifiable information. |
| **Secret** | Token, key, password, or credential. |
| **Data retention** | How long data is stored. |
| **Prompt injection** | Malicious input that tries to override model/system instructions. |
| **Jailbreak** | Attempt to bypass a model's safety constraints. |
| **Abuse monitoring** | Detecting harmful or policy-violating use. |
| **Red teaming** | Actively testing systems for failures or vulnerabilities. |

---

## Terms you should especially know for Anthropic/OpenAI-style work

Deterministic vs non-deterministic · tokens and context window · latency and streaming · tool use / function calling · evals · RAG · prompt injection · guardrails · alignment · hallucination · grounding · rate limits · backpressure · fan-out/fan-in · idempotency · observability · human-in-the-loop · safety mitigations · model behavior regression.

**The real signal isn't knowing the words.** It's being able to answer, for any system you touch: *what can go wrong, how do we measure it, and how do we make it safer or more reliable?*

## Related reading

- [`phase-3-engineering/06-observability.md`](phase-3-engineering/06-observability.md) — SLO/SLI/error-budget in practice.
- [`phase-4-ai-augmented-dev/`](phase-4-ai-augmented-dev/) — the LLM/AI terms above, applied to day-to-day AI-augmented development.
- [`phase-6-mastery/01-system-design.md`](phase-6-mastery/01-system-design.md) — the reliability/distributed-systems terms above, applied at system-design depth.
