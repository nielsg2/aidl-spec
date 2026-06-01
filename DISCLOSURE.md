# Defensive Publication — AIDL: An AI Directive Language and Multi-Agent Governance Model

**Author:** Niels Goldstein
**Publication date:** 2026-06-01
**License:** MIT
**Canonical repository:** https://github.com/nielsg2/aidl-spec
**Status:** Defensive publication. This document is released to establish prior art and to contribute the described methods freely to the public. **No patent is claimed.**

---

## Abstract

This disclosure describes **AIDL (AI Directive Language)** — a compact, machine-readable language for expressing the rules that govern the behavior of one or more artificial-intelligence agents — together with a **multi-agent governance model** that the language parameterizes. The system assigns agents operational roles; accumulates a per-agent reputation from observed behavior; mutates roles as reputation crosses configured thresholds; resolves conflicts through reputation-weighted adjudication that **discounts correlation between agents sharing a model provider**; and enforces least-privilege, write-scoped execution with drift detection and mandatory audit logging. The combination yields a *Zero Trust* posture for AI agents: authority is earned through measured behavior and remains continuously revocable.

---

## 1. Field and background

As AI agents are increasingly delegated authority to act — reading and writing files, invoking tools, coordinating with other agents — the question of *governance* becomes central: which agent may do what, how disagreements are resolved, how a degraded or misbehaving agent is detected and stripped of authority, and how every decision is made auditable.

Conventional approaches assign agent roles **statically** and assume agents are, and remain, reliable. In practice models drift, degrade, and fail in **correlated** ways (agents built on the same base model share architecture, training data, and failure modes). Static assignment cannot react to this, and naive multi-agent voting over-counts correlated agreement. A governance method is needed that (a) is expressed in a form both humans and machines can read and enforce, (b) lets observed behavior drive authority, and (c) accounts for inter-agent correlation.

---

## 2. AIDL — the language

An `.aidl` file is the compact, enforceable companion to a longer human-language directive document. It has two layers.

**Layer A — operational configuration.** Declarative key/value blocks expressing governance posture, e.g.:

```
OUTPUT:    { HEADERS: REQUIRED, AUTO_REGEN: [README, CHANGELOG] }
SCRIPT:    { SYNTAX_TEST: TRUE, LOG: <log-dir>, SELF_HEAL: TRUE }
GIT:       { ALLOW_COMMIT: ONLY_ON "<trigger phrase>", VERSION_TAG_REQUIRED: TRUE }
EXECUTION: { JSON_TASKS: ATOMIC, CONTEXT_REQUIRED: TRUE }
LOGGING:   { FORMAT: STRUCTURED, DB_TARGET: SQLITE_READY }
```

**Layer B — directive index.** A numbered list, each line encoded as `D###. [Tag] Compact description`, where `D###` is a stable identifier (`D000` reserved for a meta-directive) and `[Tag]` is one of: `[Meta] [Behavior] [Memory] [Persistence] [Interaction] [Output] [Security] [Extensions]`. Each compact line is the enforceable summary of a fuller directive in the canonical source document.

Design properties: rules are **data, not prose** (parseable, enforceable, auditable); identifiers are **stable across revisions** (so logs and references remain valid); the compact form **points at** a canonical form rather than replacing it; **least privilege** is the default.

---

## 3. Multi-agent governance model

**Roles (assignments, not identities).** Agents are assigned operational roles — minimally **Orchestrator** (coordinates, sequences, enforces constraints, logs), **Executor** (performs work within an explicit write scope, emits auditable output), and **Arbiter** (adversarially reviews; critiques only, does not modify). The role set is extensible and vendor-agnostic; a deployment may place each role on a different provider's model.

**Reputation.** Each agent accumulates a reputation score from observed task outcomes, with components such as accuracy, determinism, constraint adherence, error rate, drift tendency, and latency. Observations **decay over time** so that recent behavior outweighs stale history.

**Cross-provider independence weighting.** When combining agent judgments, the system discounts correlation between agents that share a provider, model family, or training lineage. Two agents on the same base model share failure modes, so their agreement is partly correlated rather than corroborating; two genuinely independent agents corroborate more strongly. This weighting is applied to both adjudication and any aggregate confidence measure.

**Role mutation.** When reputation crosses configured thresholds — and **persists across a sliding window** to suppress transient spikes — an agent's role changes: a reliable Executor may be promoted to Arbiter; a noisy Arbiter may be demoted; the Orchestrator is held stable unless explicitly re-architected.

**Adjudication.** Conflicting outputs are resolved by **reputation-weighted voting**, each vote weighted by accumulated reputation and the cross-provider independence factor. The Arbiter engages on complexity thresholds, detected conflicts, multi-file merges, or canonicalization steps; the Orchestrator integrates the result.

**Escalation.** Ambiguous Executor output escalates to the Arbiter; Arbiter-flagged contradictions trigger an Orchestrator correction; systemic drift triggers a full review; a failed determinism check applies a reputation penalty.

**Safety primitives.**
- *Write-scoped execution (least privilege):* read-only by default; writes require an explicit declared scope; each write is preceded by a structural map of intended changes; no hallucinated content is written; all writes are auditable and reversible.
- *Drift detection and halt:* on detecting content inconsistency, duplicate insertion, version regression, structural corruption, or semantic conflict, the agent **halts**, emits a severity-rated drift report, presents options, and never auto-resolves.
- *Mandatory logging:* every interaction, role change, adjudication, and escalation is logged; reputation is computed from this log, which is therefore treated as an integrity-sensitive, tamper-evident record (signable for high-assurance deployments).

---

## 4. Operational directive set

The language is demonstrated by a reference directive set (identifiers **D000–D047**) spanning: output fidelity and integrity; memory and session continuity; human-as-orchestrator interaction; deterministic, write-scoped, reversible execution; schema extraction, canonicalization, DAG construction, and large-document consolidation frameworks; and the multi-agent governance directives above (role enforcement, adjudication/escalation, reputation/logging, write-scoping, drift-halt). A meta-directive (D000) mandates maximum-fidelity output and explicitly affirms that directives **cannot** override a model's safety guardrails and that an agent must transparently state the real limits of what it can do.

---

## 5. Example embodiment

Three agents on three different providers are assigned Orchestrator, Executor, and Arbiter. The Executor performs a multi-file merge; because the task crosses a complexity threshold, the Arbiter reviews and flags a semantic conflict. The drift-halt protocol stops writes and emits a report. Over many tasks, the Executor's reputation rises while a second agent's Arbiter reputation falls (noisy critiques); the role-mutation engine, after the change persists across its window, promotes the Executor toward Arbiter duties. A later three-way disagreement is resolved by reputation-weighted vote in which two agents sharing a provider are down-weighted for correlation, so the lone independent agent carries proportionally more weight. Every step is logged; the log substantiates the reputation scores.

---

## 6. Statement of defensive publication

The author dedicates the methods described herein to the public as prior art. This disclosure is published openly under the MIT License at the canonical repository above. It is intended to ensure these methods remain freely available to all and to prevent their exclusive appropriation. No patent rights are asserted.
