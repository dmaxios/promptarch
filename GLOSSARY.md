# Glossary

Terminology used across PROMPTARCH APRs. Definitions here are framework-level — individual APRs MAY refine a term within their own scope, but MUST NOT contradict the framework-level meaning.

## Agent

A *stateful actor* with identity, authority, and routing logic. Agents decide what to do next, hold context across turns, and may invoke skills, tools, or other agents. Contrast with **skill**.

## Agentic AI

A class of AI systems in which one or more LLM-based agents make decisions, invoke tools or other agents, and produce consequential actions — as opposed to single-turn question-answer systems. PROMPTARCH targets the architecture of such systems.

## APR — Architectural Principle Record

A durable, numbered, citable description of one architectural principle for promptware. See [`README.md`](README.md) for the contrast with ADRs.

## ADR — Architectural Decision Record

A point-in-time decision record for one project. PROMPTARCH uses ADRs only in [`meta/decisions/`](meta/decisions/) for decisions about the project itself; everything in [`principles/`](principles/) is an APR.

## Behavioral content

Prompts, skill prose, agent definitions — the imperative content an LLM reads to know *what to do*. Contrast with **non-behavioral content** (concepts, values, rules, shapes, examples, evals). The distinction is the central premise of APR-002 OBSERVE.

## Generative contributor

A generative (typically LLM-based) system that materially contributed to producing an APR's content, recorded in the optional `generative-contributors:` frontmatter field. A generative contributor is distinct from a **principal**: it generates content under a principal's direction but is not accountable for the work and is not an author. The distinction separates the *generation* axis (what produced the text) from the *accountability* axis (who is responsible).

## Harness

The runtime scaffolding around an LLM agent — the loop that assembles a context window, calls the model, dispatches tools, manages memory and state, enforces permissions, and orchestrates delegation. In promptware the harness *is* the runtime. PROMPTARCH does not specify how to build one; [APR-018](principles/APR-018-runtime-contract.md) collects the runtime obligations a conforming harness must satisfy. Broader than the **Loader / Orchestrator**, which is the injection/resolution slice of a harness.

## Loader / Orchestrator

The runtime component that sits between "decide to delegate to a skill" and "send a prompt to the LLM." The loader resolves declared references, injects content, and writes audit-log entries. Several APRs assume a loader exists; platforms without one cannot conform to those APRs without first adding one.

## Multi-agent system

A system in which two or more agent loops act as **distinct principals** — independently scoped authority, and/or a control plane not owned by a single parent — such that **authority composes across the boundary**. A system is multi-agent by *principal*, **not** by topology: the number of LLM calls, personas, processes, or containers does not decide it. The classification is **per run**, and any output of one loop entering another's context is **untrusted content** ([APR-005](principles/APR-005-trust-boundaries.md)) regardless. Being multi-agent is what obliges cross-boundary authority analysis (reachable-graph permissions, delegation-narrows-privilege, distributed degradation budget), governed by [APR-012](principles/APR-012-federated-composition.md); the line is drawn normatively in [APR-006](principles/APR-006-composition-topology.md). Contrast with **structured agent**.

## Principal

The accountable entity on whose behalf work is performed. In the **authorship** sense, the principal defines a work's intent, governs its creation, validates its content, and assumes responsibility for its publication — recorded in the `principals:` frontmatter field (one or more); contrast with **generative contributor**. In the **runtime / authority** sense used to classify agent systems ([APR-006](principles/APR-006-composition-topology.md)), a principal is the accountable holder of one credential set and permission envelope — one **trust domain** — under which agent loops act. Both are the same principal/agent concept from agency and agentic-AI authorization, applied to *authorship* and to *execution* respectively; the work may be performed by humans, AI systems, or both, so *principal* names the accountable role, not the act.

## Promptware

Software whose dominant behavior is shaped by prompts and natural-language specifications consumed by LLM agents, rather than by deterministic code paths. Editing a Markdown or YAML file in a promptware system can materially change runtime behavior, because that file is loaded into an agent's context at execution time.

The full position — what promptware is, what it is NOT, and why it requires its own architectural discipline — is in [APR-000 PROMPTWARE](principles/APR-000-promptware.md). This glossary entry is a one-paragraph summary; the APR is the citable canonical statement.

## Skill

A *stateless transform* with schema-bound inputs and outputs. Skills do one thing, repeatably, with declared I/O contracts. Contrast with **agent**.

## Structured agent (with subagents)

A single agent whose constituent loops all share **one principal** — one credential set, one permission envelope, one **trust domain** — and **one deterministic control point** owning loop termination, budget, and halt. Its **subagents** decompose *attention*, not *authority*, so it owes only context-laundering discipline within its one envelope, not cross-boundary authority analysis. Attaching a component that holds its own credentials (e.g. an MCP server that is itself an agent) turns it into a **multi-agent system** at runtime — the classification is per run. Defined normatively in [APR-006](principles/APR-006-composition-topology.md). Contrast with **multi-agent system**.

## Subagent

A loop invoked by a parent agent — through the tools interface, with an isolated context — that **shares the parent's principal** (credentials, permission envelope, trust domain). A subagent is recursive delegation within one envelope ([APR-006](principles/APR-006-composition-topology.md)), not a distinct principal: it decomposes *attention*, not *authority*. A subagent's output re-entering the parent's context is still **untrusted content** ([APR-005](principles/APR-005-trust-boundaries.md)) — sharing a principal grants no trust exemption.

## Trust domain

The boundary within which one **principal**'s authority applies — one credential set and one permission envelope. Loops inside a trust domain share authority (a **structured agent**); authority that **composes across** a trust-domain boundary makes a **multi-agent system** ([APR-012](principles/APR-012-federated-composition.md)). The trust-domain boundary is the runtime face of the **containment vs. dependency** line ([APR-019](principles/APR-019-identity.md)): containment stays within one domain, a dependency crosses into a foreign one.

---

Additional terms are defined within the APR that introduces them. Where a term is shared across multiple APRs, it should be promoted to this glossary.
