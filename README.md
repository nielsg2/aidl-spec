# AIDL — AI Directive Language

**A compact, machine-readable language for governing AI agent behavior — roles, reputation, adjudication, escalation, and audit logging — paired with a runtime model for multi-agent governance.**

AIDL lets you declare *how* AI agents should behave and be governed in a format that is both human-readable and machine-parseable, so the rules travel with the session instead of living in someone's head. It pairs a terse directive-index format (`.aidl`) with a multi-agent governance model: assign agents roles, accumulate per-agent reputation from observed behavior, mutate roles as reputation changes, and adjudicate conflicts through an explicit escalation path — with every decision logged.

## Contents

| File | What it is |
|------|------------|
| [`SPEC.md`](SPEC.md) | The AIDL language: two-layer format, grammar, tags, worked example |
| [`GOVERNANCE.md`](GOVERNANCE.md) | The multi-agent governance model — the core idea |
| [`DIRECTIVES.md`](DIRECTIVES.md) | The full operational directive set (D000–D047) |
| [`PROVENANCE.md`](PROVENANCE.md) | Origin, timeline, and why this is published openly |

## Why this exists

Autonomous and semi-autonomous AI agents are increasingly trusted to act. Without explicit, auditable governance — who may do what, how disagreements are resolved, how a misbehaving agent is caught and demoted — that trust is unearned. AIDL is one attempt at a **Zero Trust posture for AI agents**: never trust an agent's output implicitly; verify it, govern it, log it.

## Status

Published openly as a contribution and as prior art. This is not a product — it is a specification and a set of design patterns, offered freely for anyone building agent systems. See [`PROVENANCE.md`](PROVENANCE.md) for the origin timeline and the reasoning behind open publication.

## License

MIT — see [`LICENSE`](LICENSE). Copyright (c) 2025 Niels Goldstein.
