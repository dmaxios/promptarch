# APR-025 — Governance Attribution — Digest

> **Generated digest of [APR-025 — A Governance-Attribution Principle for Promptware](../APR-025-governance-attribution.md) v0.1.0.** The full APR is authoritative — read it for motivation, prior art, and worked detail. Do not edit by hand.

**Abstract.** Every output is attributable to the governance envelope that produced it — an evidence-bound, append-only record derived mechanically at the routing decision, never the model's own claim. Unattributed output is ungoverned by definition; attribution records, it never gates.

**Principle.** Attribution is derived, recorded, and legible — never claimed. The envelope class is computed from mechanical facts at the routing decision, written to an append-only session-scoped record, bound to any in-band announcement; a turn with no record was not governed (absence-as-evidence). Completes the absence-as-fact doctrine: artifact (APR-017) → verdict (APR-024) → **interaction**.

## Attribution classes (enum, never boolean)

| Class | Produced by | Meaning |
| --- | --- | --- |
| **governed** | full envelope (planned, approved, gated, run-recorded) | all guarantees apply |
| **advisory** | declared out-of-envelope execution (e.g. standalone) | guarantees explicitly downgraded |
| **degraded** | envelope with recorded gaps (APR-024 `pass-with-gaps`) | governed, with enumerated holes attached |
| **operational** | platform tooling | no methodology claim |
| **native** | harness model; promptware dispatched nothing | ungoverned |

A boolean "framework ✅" collapses governed/advisory/degraded — erasing exactly the distinction an auditor needs; it is the vacuous pass in UI form.

## Normative rules

- Class **derived from mechanical facts** at the routing decision (plan approved / advisory declared / verdict gaps / tooling / nothing dispatched); the model MUST NOT judge its own class (APR-003 §5.1).
- Enum MUST distinguish at least the five classes; the enum is **closed** — extension is an amendment, not a keyboard judgment.
- Every routed turn appends to an **append-only, session-scoped** record (session-scoped: ungoverned turns have no run to record into); records referencing runs inherit their audit chains. Session history MUST reconstruct from the record alone, offline.
- **Absence-as-evidence**: a turn with no record was not routed by the promptware — that inference is the mechanism.
- **Co-write**: any in-band announcement is bound to its record — emitted only after the evidence exists, written together or neither. An announcement that can print without its record is non-conformant.
- **Never gates**: attribution records and announces; it MUST NOT block a turn.
- **The padlock caveat is normative**: consumer docs MUST state that absence of an ungoverned-warning is not proof of governance for turns the promptware never saw.
- Degraded/advisory attribution MUST NOT launder: gaps stay attached (APR-024); downgraded guarantees are named.

## Runtime obligation (APR-018 R15)

The sub-promptware case (promptware never loaded) is unreportable from inside. Registered as **R15**: a conforming harness SHOULD stamp every consumer-visible turn with turn-provenance. Until then: positive attribution reliable, complete accounting honestly partial.

## Governance checks

Class derivation is mechanical (no prompt assigns it) · enum not boolean, no collapsed rendering · co-write atomic (no evidence-naming announcement before the evidence) · record append-only, session-scoped, offline-reconstructable · no code path gates on attribution · the absence caveat documented.

## Scope limits — do NOT misapply

Not a UX/badge spec (mandates existence + evidence-binding + legibility, not looks) · not AI-disclosure compliance (implements such duties' mechanism — as APR-009 implements Art. 14) · not a gate · not run-level observability (APR-011 traces inside governed runs; this attributes whether/which envelope ran at all) · not a tamper-evidence guarantee (session record attests dispatch decisions; run-referencing records inherit run chains).

---
*Source: [APR-025 — A Governance-Attribution Principle for Promptware](../APR-025-governance-attribution.md) v0.1.0 · regenerate this digest whenever the source changes.*
