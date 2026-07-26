# APR-006 — Composition & Delegation Topology — Digest

> **Generated digest of [APR-006 — A Composition and Delegation-Topology Principle for Multi-Agent Promptware](../APR-006-composition-topology.md) v0.2.0.** The full APR is authoritative — read it for motivation, prior art, and worked detail. Do not edit by hand.

**Abstract.** Compose agents and skills into a bounded delegation graph: prefer skills, keep delegation acyclic with bounded feedback loops, declare the edges and traverse them dynamically, guarantee termination, and narrow authority along each edge — so control flow stays legible, terminating, and auditable.

**Principle.** A multi-agent system is a delegation graph (nodes = agents/skills, edges = delegations). That graph MUST be explicit and bounded — in shape, depth, and authority. Each edge is an ASPECT delegation contract.

## Classifying the system: structured agent vs. multi-agent

Decided by **principal, not topology** (not call/persona/process/container count). **Structured agent (with subagents):** all loops share **one principal** — one credential set, permission envelope, trust domain — and one control point owning termination/budget/halt; subagents decompose *attention*, not *authority*. **Multi-agent:** two+ loops are **distinct principals**, so **authority composes across the boundary**. Per-run test: (1) same credentials? (2) one deterministic termination/budget/halt owner? (3) does halting one quiesce the rest? Any **no** ⇒ multi-agent ⇒ **MUST** do cross-boundary authority analysis (APR-012). Per-run, not static (an MCP server holding its own credentials flips it at runtime). Both sides: a loop's output into another's context is **untrusted content** (APR-005) — the line decides whether *authority* composes, never *trust*. Runtime face of containment-vs-dependency (APR-019).

## The four axes

- **Granularity** — a unit SHOULD be a skill unless it needs identity, authority, or routing (then it's an agent, per ASPECT). Prefer the smallest graph; fight god-agents and skill sprawl.
- **Topology** — forward delegation SHOULD be a DAG (supervisor/worker); **feedback loops** (reflexion, ReAct, evaluator–optimizer, debate) are **first-class** but MUST be explicitly declared with a termination condition. Bounded loop ≠ unbounded cycle (the latter is forbidden).
- **Legibility** — the *allowed* edges (who MAY call whom) MUST be declared (the envelope); the *actual* path MAY be dynamic within it; every actual delegation MUST be audit-logged.
- **Bounds** — termination + authority (below).

## Normative rules

- A unit of behavior SHOULD be a skill unless it needs identity/authority/routing; prefer the smallest graph.
- Forward delegation SHOULD form a DAG; feedback loops are first-class but MUST be declared with a termination condition; unbounded cycles are forbidden.
- The allowed delegation edges MUST be declared; runtime delegations MUST stay within the envelope; the actual path MAY be dynamic.
- Every actual delegation MUST be audit-logged (`caller`, `callee`, input reference, timestamp).
- Termination MUST be guaranteed by composing depth + cycle-detection + budget bounds; tripping any MUST halt with an audit-logged error (never silently truncate or spin).
- A delegate's authority/blast-radius MUST be bounded by its caller's (attenuation, never escalation); raising authority MUST be an explicit upward escalation; delegate output is untrusted input (APR-005).
- Routing/dispatch follows APR-003: deterministic where a closed-form choice exists, prompt-driven only for genuine judgment.

## Governance checks

Declared envelope present and reviewed · forward delegation acyclic, feedback loops declared with a termination condition (no unbounded cycles) · termination bounds (depth/cycle/budget) configured · authority monotonic across edges (escalations explicit) · delegation audit log reconcilable against the envelope · new agents justified by an identity/authority/routing need.

## Scope limits — do NOT misapply

Not a runtime/scheduler/message bus · not a multi-agent framework (framework-agnostic) · not a replacement for ASPECT (ASPECT governs nodes/edges; this governs the graph) · not a guarantee of correct routing · not applicable to single-component systems (no graph).

---
*Source: [APR-006 — A Composition and Delegation-Topology Principle for Multi-Agent Promptware](../APR-006-composition-topology.md) v0.2.0 · regenerate this digest whenever the source changes.*
