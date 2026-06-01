# Multi-Agent Governance Model

This is the core of AIDL: a model for governing a set of AI agents — potentially
running on **heterogeneous providers** — so the group is more trustworthy than any
single agent. The guiding stance is Zero Trust: an agent earns authority through
observed behavior, and never holds it unconditionally.

---

## Roles

Agents are assigned operational roles. Roles are **assignments, not identities** —
an agent can be promoted or demoted between them (see *Role Mutation*).

- **Orchestrator** — coordinates workflow, maintains global context, sequences
  tasks, enforces constraints, and logs inter-agent interactions. Issues work to
  Executors and routes disputes to Arbiters.
- **Executor** — performs the actual work (file operations, transforms,
  generation) under the Orchestrator's direction. Operates within an explicit
  write scope and emits structured, auditable output.
- **Arbiter** — reviews and adversarially evaluates Executor output. Flags
  contradictions, drift, missing constraints, and ungrounded assumptions.
  Provides critique only; does not modify artifacts. Used for high-complexity
  adjudication and tie-breaking.

> The role set is extensible. Orchestrator / Executor / Arbiter is the minimal
> useful triad, not a fixed ceiling.

A concrete deployment might place each role on a different vendor's model — this
is illustrative, not prescriptive. The model is vendor-agnostic by design.

---

## Reputation

Each agent accumulates a **reputation score** from observed task outcomes. Suggested
components:

| Component | Measures |
|-----------|----------|
| Accuracy | Correctness of output against ground truth or review |
| Determinism | Reproducibility of output for equivalent input |
| Constraint adherence | Whether the agent respected stated bounds |
| Error rate | Frequency of failed or rejected work |
| Drift detection | Tendency to wander from task or prior state |
| Latency | Time-to-completion |

Reputation observations should **decay over time** — recent outcomes weigh more
heavily than old ones, so accumulated history cannot mask degraded current
behavior.

### Cross-provider independence weighting

When agents run on different underlying models, their judgments are not fully
independent: two agents on the **same** base model share architecture, training
data, and failure modes, so their agreement is partly correlated rather than
corroborating. When combining agent judgments, **discount correlation between
agents that share a provider, model family, or training lineage.** Two genuinely
independent agents corroborate more than two siblings of the same model.

---

## Role mutation

When an agent's reputation crosses configured thresholds, its role can change:

- A consistently high-reliability **Executor** may be promoted to **Arbiter**.
- A noisy or hallucinating **Arbiter** may be demoted.
- The **Orchestrator** is held stable unless explicitly re-architected.

Mutation should require **persistence over a sliding window** — a transient spike
must not trigger a role change. Threshold values are policy, declared in the
`.aidl` configuration.

---

## Adjudication

When agents produce conflicting outputs, resolve via **reputation-weighted
adjudication**: each agent's vote is weighted by its accumulated reputation and by
the cross-provider independence factor above. The Arbiter engages when:

- a complexity threshold is exceeded,
- a conflict is detected,
- a multi-file merge or schema rewrite occurs, or
- a canonicalization / consolidation step requires validation.

The Orchestrator integrates the Arbiter's findings into the next step.

---

## Escalation

- Executor output ambiguous → escalate to Arbiter.
- Arbiter flags contradictions → Orchestrator issues a corrective directive.
- Orchestrator detects systemic drift → trigger a full review.
- Any agent fails a determinism check → apply a reputation penalty.

---

## Safety primitives

- **Write-scoped execution (least privilege).** Read-only by default. Any write
  requires an explicit scope. Before writing, the Executor emits a *structural
  map* of what it intends to change.
- **Drift detection and halt.** On detected drift, the agent **halts**, emits a
  drift report, and presents options. It must not silently auto-resolve.
- **Mandatory logging.** Every inter-agent interaction, role change, adjudication,
  and escalation is logged. Reputation is computed from this log, so the log is a
  governed, integrity-sensitive artifact. (For high-assurance deployments, sign
  log entries; treat the log as a tamper-evident record.)

---

## Why reputation-driven governance

Static role assignment assumes agents are reliable and stay reliable. They are
not, and they do not — models drift, degrade, and fail in correlated ways.
Letting **observed behavior** drive authority, with independent review and an
audit trail, turns a set of imperfect agents into a system whose trust is earned,
measured, and revocable. That is the whole point.
