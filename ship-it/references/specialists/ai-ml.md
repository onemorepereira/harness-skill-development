# AI / ML Specialists

Load when the change calls a language model or other ML model, adds prompts, agents, tools for agents, retrieval (RAG), embeddings, model training or fine-tuning, or features whose output is probabilistic.

---

## ML / AI Engineer

**Activate when:** model calls, prompts, agent loops, retrieval, embeddings, or model selection.

| Stage | Lens |
|---|---|
| Define | What does "good output" mean? Write it as criteria that can be checked on examples. What happens when the model is wrong, and who sees it? |
| Design | Model choice against quality, latency, and cost. Prompt structure, tool definitions, context limits. Fallbacks when the model fails, times out, or returns malformed output. Human review where mistakes are costly. |
| Build | Prompts versioned in the repo. Structured output with validation. Timeouts and retries. Log inputs and outputs for debugging, respecting privacy rules. |
| Verify | An evaluation set of realistic examples, including hard and adversarial cases, scored against the criteria from Define. Compare against the current version or a baseline. Run several times to see variance. |
| Launch | Monitor quality signals, cost, and latency. Roll out gradually. |
| Evolve | Add real failures to the evaluation set. |

**Red flags:** "it worked on the three examples I tried"; parsing free-form model text without validation; no fallback on failure; prompt changes shipped without re-running evaluations.

**Gate evidence:** evaluation results against the baseline.

### Reviewer Injection — ML / AI Engineer

Append to reviewer prompts when this specialist is active.

- Is "good output" defined as criteria that can be checked on examples, not subjectively?
- Are prompts versioned in the repo?
- Is there a fallback when the model fails, times out, or returns malformed output?
- Has an evaluation set been run — including hard and adversarial cases — and compared to a baseline?

---

## AI Safety Reviewer

**Activate when:** model output reaches users directly, an agent can take actions (write, send, delete, spend), or untrusted content (web pages, emails, documents, user uploads) goes into a prompt.

| Stage | Lens |
|---|---|
| Design | Treat content from untrusted sources as data, not instructions (prompt-injection risk). Limit what tools an agent can call, and require confirmation for irreversible actions. Decide what the model must refuse. |
| Verify | Test with injected instructions in untrusted inputs, attempts to misuse tools, and requests the feature should decline. |

**Red flags:** an agent with broad write access acting on content from untrusted sources; model output rendered as HTML without sanitizing.

**Gate evidence:** adversarial test cases and their results.

### Reviewer Injection — AI Safety Reviewer

Append to reviewer prompts when this specialist is active.

- Is content from untrusted sources (web pages, emails, user uploads) treated as data, not instructions?
- Are irreversible agent actions gated on confirmation?
- Have adversarial test cases been run (injected instructions, tool misuse attempts)?
