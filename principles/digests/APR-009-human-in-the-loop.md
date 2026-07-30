# APR-009 — Human-in-the-Loop Oversight Placement — Digest

> **Generated digest of [APR-009 — A Human-in-the-Loop Oversight-Placement Principle for Promptware](../APR-009-human-in-the-loop.md) v0.3.1.** The full APR is authoritative — read it for motivation, prior art, and worked detail. Do not edit by hand.

**Abstract.** Place human oversight by reversibility and blast radius: irreversible/high-blast actions get plan-and-approve (before); reversible/low-blast get fire-and-judge (after). Unknown defaults to irreversible; safety floors are never sampled away; long runs batch questions to declared checkpoints.

**Principle.** An action's oversight mode is set by whether it can be undone; how heavy that oversight is, by how much it affects. Reversibility selects the mode; blast radius tunes the rigor.

## The two modes

- **Plan-and-approve** (before): the agent presents its action; a human approves/rejects/modifies before it runs. For **irreversible** actions.
- **Fire-and-judge** (after): the agent executes; a human reviews the captured action after. For **reversible** actions only (you can only judge-after what you can undo).

## The placement rule

| | Reversible | Irreversible |
|---|---|---|
| Low blast | fire-and-judge (sampled) | plan-and-approve (light/batchable) |
| High blast | fire-and-judge (notify, fast rollback, high sampling) | plan-and-approve (individual, mandatory) |

Reversibility → the column (mode); blast radius → the row (rigor).

## Normative rules

- Every overseeable action MUST have a **declared reversibility**; unknown MUST default to irreversible.
- Mode MUST be set by reversibility (irreversible → plan-and-approve; reversible → fire-and-judge); blast radius sets rigor, not mode.
- Plan-and-approve MUST show the human the **concrete diff** (not a model summary), intent, blast radius, and rollback plan, and record the approver identity, **before** execution; it SHOULD show the **predicted cost** where estimable (an operator cannot oversee a number they cannot see) — as **ranges with confidence/sample count, never unmeasured point values**; critical-path headline where dependencies are declared.
- Fire-and-judge MUST capture the action, record the judgment + reviewer identity, and feed corrections back to evals/patterns; used only for reversible actions.
- The agent MUST NOT self-assess reversibility (APR-003) — it is declared metadata.
- Fatigue levers (thresholds, sampling, batching, anchoring) MUST NOT apply to safety-critical or irreversible-high-blast actions — those always get individual approval (the hard safety floor).

## Scheduled checkpoints (long-running runs)

- Oversight interaction points MUST be **declared at plan time** and visible in the approved plan; execution is non-interactive *between* checkpoints.
- Deferrable questions arising mid-run MUST **batch to the next checkpoint** — never ad hoc, never silently dropped. Blocking questions follow APR-017 escalation (declared timeout, expiry fails closed).
- Plan-and-approve MAY be **staged at phase boundaries** (approve early phases, inspect, then decide the rest).
- **AI-substituted judging**: allowed only as an **isolated dispatch that is not the producer** of the reviewed artifact; the substitution is **recorded** as a judgment gap and NEVER counts as the human review where the safety floor requires one.

## Anchoring (fatigue lever)

*The anchor does the deciding*: a long plan pre-checked in full gets rubber-stamped. Present the **minimal default selection** with additions offered (expandable), not the maximal set pre-checked; deselection MUST be **dependency-aware** (report the cascade of an unchecked item); priced offers where estimable ("+3 more, +1 h").

## Governance checks

Reversibility declared on every overseeable action (unknown → irreversible) · mode matches reversibility · approval records show the concrete diff + approver identity · fire-and-judge corrections routed to evals/patterns · no sampling/batching/threshold/anchoring lets a safety-critical or irreversible-high-blast action skip individual approval · checkpoints declared in the approved plan, no unscheduled mid-run prompts, no dropped deferred questions · AI-substituted reviews recorded (isolated non-producer dispatch) and never satisfy the safety floor · cost visible at approval where estimates exist (ranges with measurement provenance; unmeasured point values flagged).

## Scope limits — do NOT misapply

Not a UX/workflow-engine spec · not the autonomy-level definition (ASPECT defines levels; this defines which mode a level selects) · not a guarantee against human error (ensures the human is informed and correctly placed) · not access control/authn (who *may* approve is platform authorization) · not for read-only/advisory agents (the human is already the actor).

---
*Source: [APR-009 — A Human-in-the-Loop Oversight-Placement Principle for Promptware](../APR-009-human-in-the-loop.md) v0.3.1 · regenerate this digest whenever the source changes.*
