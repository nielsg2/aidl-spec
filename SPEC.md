# AIDL Language Specification

AIDL (AI Directive Language) is a compact, machine-readable format for expressing
the rules that govern an AI agent or a swarm of agents. It is designed to be:

- **Human-readable** — a person can review the governance posture at a glance.
- **Machine-parseable** — an agent or orchestrator can load and enforce it.
- **Compact** — small enough to inject into a session as persistent context.

An `.aidl` file is the terse, operational companion to a longer human-language
directive document. The long form explains; the `.aidl` form enforces.

---

## Two layers

An `.aidl` file has two sections.

### Layer A — Operational configuration

Key/value blocks (YAML-style) that set the governance posture. Values are
declarative; an enforcing runtime reads them as policy. Example:

```
OUTPUT:
  HEADERS: REQUIRED
  AUTO_REGEN: [README.md, CHANGELOG]

SCRIPT:
  SYNTAX_TEST: TRUE
  LOG: ./logs
  SELF_HEAL: TRUE

GIT:
  ALLOW_COMMIT: ONLY_ON "<explicit trigger phrase>"
  VERSION_TAG_REQUIRED: TRUE

EXECUTION:
  JSON_TASKS: ATOMIC
  CONTEXT_REQUIRED: TRUE

LOGGING:
  FORMAT: STRUCTURED
  DB_TARGET: SQLITE_READY

CONTRIBUTORS:
  TRACK_AUTHORS: TRUE
  SCORE_REPUTATION: TRUE
```

### Layer B — Directive index

A numbered list of directives, each one line, encoded as:

```
D###. [Tag] Compact description
```

- `D###` — a stable directive identifier (zero-padded; `D000` reserved for a meta-directive).
- `[Tag]` — one classification tag (see legend).
- description — an imperative, single-sentence rule.

The directive index is a compressed pointer set: each line is the enforceable
summary of a fuller directive defined in the human-language source document.

#### Tag legend

| Tag | Domain |
|-----|--------|
| `[Meta]` | Governs the directive system itself |
| `[Behavior]` | How the agent acts / decides |
| `[Memory]` | Context, persistence, session continuity |
| `[Persistence]` | Files, repos, durable artifacts |
| `[Interaction]` | Human ↔ agent protocol |
| `[Output]` | Format and integrity of produced artifacts |
| `[Security]` | Scope, drift, safety, audit |
| `[Extensions]` | Registries, plugins, reusable capability |

#### Example directive index

```
D000. [Meta]        Maximum-fidelity execution — regenerate outputs from source; no partial or placeholder output.
D007. [Memory]      Reload directive and roadmap files at session start; assume context has degraded.
D027. [Interaction] Human-as-orchestrator — the human is the executor; the agent is advisory and procedural.
D033. [Behavior]    Multi-agent role enforcement — Orchestrator / Executor / Arbiter; no role-boundary breaches.
D035. [Memory]      Agent reputation and logging — log all interactions; reputation drives future role mutation.
D036. [Security]    Deterministic write-scoped execution — read-only by default; explicit write scope; pre-write structural map.
D037. [Security]    Drift detection and halt — on detected drift, HALT, emit a drift report, present options, never auto-resolve.
```

---

## Design principles

1. **The rules are data, not prose.** Governance you can parse is governance you can enforce and audit.
2. **Identifiers are stable.** A directive keeps its `D###` across revisions so logs and references remain valid.
3. **The compact form points at a canonical form.** `.aidl` never replaces the human-language directive document; it indexes it.
4. **Least privilege by default.** Read-only unless a write scope is explicitly granted (see `GOVERNANCE.md`).

---

## Grammar (informal)

```
aidl-file      := config-section directive-section
config-section := { BLOCK ":" newline { "  " KEY ":" VALUE newline } }
directive-line := "D" DIGIT DIGIT DIGIT "." WS "[" TAG "]" WS DESCRIPTION
```

Comments begin with `#`. Lists use `[a, b, c]`. Values are bare tokens, quoted
strings, or lists. The format is deliberately forgiving — it is meant to be
written by hand and read by both people and machines.
