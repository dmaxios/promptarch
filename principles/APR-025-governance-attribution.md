---
apr: 25
title: "A Governance-Attribution Principle for Promptware"
abstract: "Every output is attributable to the governance envelope that produced it — an evidence-bound, append-only record derived mechanically at the routing decision, never the model's own claim. Unattributed output is ungoverned by definition; attribution records, it never gates."
status: Draft
class: architectural
version: 0.1.0
principals:
  - D. Maxios
generative-contributors:
  - "Claude Fable 5 (Anthropic)"
created: 2026-07-27
last-updated: 2026-07-27
audience: Harness and platform builders; framework authors whose promptware executes inside a general assistant harness; auditors reconstructing what governed a session
supersedes: []
superseded-by: []
related:
  - APR-003
  - APR-009
  - APR-011
  - APR-017
  - APR-018
  - APR-024
tags:
  - attribution
  - provenance
  - transparency
  - governance-envelope
  - absence-as-evidence
---

# APR-025 — A Governance-Attribution Principle for Promptware

> **Every consumer-visible output is attributable to the governance envelope that produced it — from an evidence-bound, append-only record derived mechanically at the routing decision, never from the model's own claim. Unattributed output is ungoverned by definition (absence-as-evidence), and attribution records — it never gates.**

*Injectable summary (for feeding to an LLM): [`digests/APR-025-governance-attribution.md`](digests/APR-025-governance-attribution.md). This full APR is canonical.*

## Motivation

Promptware executes inside a harness whose model can also answer **natively** — no plan, no approval, no gates, no provenance. Both kinds of answer arrive as prose in the same session, in the same voice, and the consumer cannot tell them apart. That makes every governance claim **unfalsifiable from the consumer's seat**: an answer produced under plan-approval, security gates, and a hash-chained run record, and one produced by the bare model, are different *kinds* of object presented identically. The risk grows with every advisory or standalone execution path a platform adds — advisory output silently reads as governed output.

Three failure modes, each observed in the field:

- **The boolean badge.** "Framework ✅" collapses *fully governed*, *advisory*, and *degraded* into one signal — and the distinction it erases (envelope versus advisory) is precisely the one an auditor needs. A boolean badge on advisory output is [APR-024](APR-024-coverage-honest-verdicts.md)'s vacuous pass in UI form: absence of governance rendered as presence of framework.
- **The self-attested banner.** A model announcing its own governance status is a component adjudicating a property that licenses its own authority — the [APR-003 §5.1](APR-003-code-prompt-boundary.md) no-self-adjudication violation. A banner that can be printed without the thing it announces having happened is a decoration wearing an attestation's clothes.
- **The padlock problem.** Once a positive indicator exists, its *absence* gets over-trusted (the browser security-indicator lesson). Below the promptware layer — a turn where the orchestrating promptware was never loaded — nothing framework-side can self-report, so complete accounting is a *runtime* obligation, never a promptware promise.

The corpus already holds the doctrine that resolves this — **the absence of a thing is recorded as a fact** — but applies it at only two layers: the artifact layer ([APR-017](APR-017-graceful-degradation.md) boundary marks) and the verdict layer (APR-024 coverage). The interaction layer, where a human actually consumes the output, has no owner. This APR completes the symmetry: **artifact → verdict → interaction.**

## The principle

> **Attribution is derived, recorded, and legible — never claimed.** The envelope class of a turn is computed from mechanical facts (a plan was approved; a component declares advisory execution; a run carries gaps; nothing was dispatched) at the point where routing is decided. It is written to an append-only, session-scoped record; any in-band announcement is bound to that record; and a turn with no record was not governed — that inference is the mechanism, not a gap in it.

## The attribution classes

The class set is platform-defined but **MUST be an enum, never a boolean**, and MUST at minimum distinguish:

| Class | What produced the output | Governance meaning |
| --- | --- | --- |
| **governed** | the full envelope — planned, approved, gated, run-recorded | every platform guarantee applies |
| **advisory** | a component executing outside the envelope by declaration (e.g. standalone eligibility) | guarantees explicitly downgraded — output is advice, not governed product |
| **degraded** | the envelope ran, with recorded gaps ([APR-024](APR-024-coverage-honest-verdicts.md) `pass-with-gaps`, [APR-017](APR-017-graceful-degradation.md) degradations) | governed, with enumerated holes that travel with the output |
| **operational** | platform tooling (list/show/validate) | no methodology claim to attribute |
| **native** | the harness's model answered; the promptware dispatched nothing | ungoverned |

Collapsing *governed*, *advisory*, and *degraded* into one positive signal destroys the distinction attribution exists to carry. `advisory` and `degraded` are not new states — platforms already record them on artifacts and verdicts; this APR only requires that the interaction-level announcement not erase them.

## Scope and applicability

### When this applies

- Any system where a consumer-visible output can be produced under **more than one governance envelope** — full pipeline, declared-advisory execution, gap-carrying runs, operational tooling, or the native model. This is essentially every promptware platform hosted in a general-purpose harness.

### When this does NOT apply

- **Single-envelope systems** — where every output passes the same governance, there is nothing to distinguish (though the ungoverned/native case rarely stays absent for long).
- **UX specification** — this APR mandates that attribution exist, be evidence-bound, and be legible at consumption; it does not prescribe banners, badges, or rendering.
- **Generic AI-disclosure regulation** — "this content was AI-generated" duties (e.g. EU AI Act Art. 50) are the regulatory cousin; this APR supplies a placement-and-evidence *mechanism* such duties can be implemented with, the same relationship [APR-009](APR-009-human-in-the-loop.md) has to Art. 14.

## Prescription

- The attribution class of every consumer-visible turn **MUST** be **derived from mechanical facts** at the routing decision — a plan was approved and dispatched; a component declares advisory execution; the run's verdict carries gaps; a tooling command ran; nothing was dispatched. The model **MUST NOT** judge its own class (APR-003 §5.1).
- The class set **MUST** be an enum distinguishing at least governed / advisory / degraded / operational / native; a boolean "framework: yes/no" signal is non-conformant. The enum is **closed** — extending it is an amendment to the platform's attribution contract, not a judgment call at the keyboard.
- Every routed turn **MUST** append its attribution record to an **append-only, session-scoped** store — session-scoped because ungoverned turns have no run to record into, and a run-scoped store would make the record impossible exactly where it matters most. Records referencing a run inherit that run's audit chain ([APR-011](APR-011-observability.md), [APR-002](APR-002-observe.md)).
- **Absence-as-evidence** is the reconstruction rule: a turn with no attribution record was not routed by the promptware. Reconstruction of a session's attribution history **MUST** be possible from the record alone, offline and deterministically.
- Any **in-band announcement** (banner, badge, prefix) **MUST** be bound to the record: an announcement naming evidence (a run id, a plan) is emitted only after that evidence exists, and the announcement and its record are written together or neither. An announcement that can print without its record is non-conformant.
- Attribution **MUST NOT gate**: it records and announces, and never blocks a turn. A system that refuses to answer because it cannot classify itself has converted transparency into an availability defect.
- The platform's consumer-facing doctrine **MUST state the limit plainly**: positive attribution is reliable; the *absence* of an ungoverned-warning is **not** proof of governance for turns the promptware never saw. Omitting this statement manufactures the false confidence the mechanism exists to remove (the padlock problem).
- Degraded attribution **MUST NOT launder**: a `degraded` turn's gaps remain attached per APR-024 propagation; an `advisory` turn's downgraded guarantees are named, not implied.

## Runtime obligations

The sub-promptware case — a turn in which the orchestrating promptware was never loaded — is unreportable from inside the promptware, for the structural reason that the promptware never built the request. Closing it is a harness concern, registered as [APR-018](APR-018-runtime-contract.md) **R15** (owned here, indexed there): a conforming harness **SHOULD** stamp every consumer-visible turn with a turn-provenance record (which promptware, if any, was in the loop), completing the attribution ledger for turns the promptware cannot see. Until a harness provides R15, a platform's accounting is honestly partial — positive attribution reliable, complete accounting deferred — and its doctrine says so.

## Governance and validation

The shared governance model — two-tier enforcement, audit-log binding, and change-via-ADR — is defined in [APR-010](APR-010-governance.md); the checks below are this APR's domain-specific additions.

A conformant platform checks, in review or CI:

- **Derivation, not judgment** — each attribution class maps to a mechanical predicate over recorded facts; no prompt content assigns a class (Tier 1 schema/code review).
- **Enum, not boolean** — the attribution surface distinguishes at least the five classes; no rendering collapses governed/advisory/degraded into one positive signal (Tier 2).
- **Co-write enforced** — an announcement referencing evidence cannot be emitted before the evidence exists; announcement and record are atomic (Tier 1 check).
- **Append-only, session-scoped, reconstructable** — the record store is `O_APPEND`-disciplined, spans runless turns, and a session's history renders from it offline (Tier 1).
- **Never gates** — no code path blocks a turn on attribution failure (Tier 1).
- **The limit is documented** — consumer-facing doctrine states what the absence of a warning does and does not prove (Tier 2).

## What this principle is NOT

- **Not a UX or badge specification.** It mandates existence, evidence-binding, and legibility of attribution — not its look.
- **Not AI-disclosure compliance.** It implements such duties' mechanism; it does not restate their policy.
- **Not a gate.** Attribution never blocks; enforcement of *what may run ungoverned* belongs to the platform's authorization layer, not to the transparency record.
- **Not run-level observability.** [APR-011](APR-011-observability.md) traces what happened *inside* a governed run; this attributes *whether and which* envelope ran at all — the record that exists precisely when APR-011's doesn't.
- **Not a tamper-evidence guarantee.** The session record attests the promptware's own dispatch decisions; records referencing runs inherit those runs' chains, but a local session file does not itself promise tamper-proofness.

## Relationship to established patterns

| Pattern | What it shares with this APR | What this APR adds |
| --- | --- | --- |
| **SLSA / in-toto attestations** (supply-chain) | A claim about how an artifact was produced is bound to evidence, not asserted at delivery | Applied to *interaction turns*, with the ungoverned class first-class and inferable from absence |
| **C2PA content credentials** | Provenance travels with content to the point of consumption | The provenance is the *governance envelope*, not authorship; enum-not-boolean preserves the advisory/governed line |
| **Browser security indicators** (Felt et al.; Schechter et al.) | Positive indicators are over-trusted in their absence | The limit is a normative documentation requirement, not a discovered incident |
| **Append-only audit trails / WORM logs** | Durable, diffable, handable evidence surviving the session's ephemera | Session-scoped placement so runless (ungoverned) turns have a home; absence-as-evidence as the read rule |
| **EU AI Act Art. 50** (transparency duties) | Required disclosure of machine involvement | A concrete placement-and-evidence mechanism that implements such duties (as APR-009 implements Art. 14) |
| **In-corpus:** [APR-003 §5.1](APR-003-code-prompt-boundary.md), [APR-017](APR-017-graceful-degradation.md), [APR-024](APR-024-coverage-honest-verdicts.md), [APR-018](APR-018-runtime-contract.md) | No self-adjudication; three-audience disclosure; absence-as-fact at the verdict layer; the runtime-obligation vehicle | The interaction layer joins the doctrine: artifact → verdict → **turn**, one rule applied at all three |

The novel contribution is a **promptware-specific attribution discipline for the interaction layer**: envelope classes derived from mechanical facts, bound to an append-only session record with a co-write constraint, read under absence-as-evidence — so governed and ungoverned output are distinguishable objects at the point of consumption, and the platform's central governance claim becomes falsifiable from the consumer's seat.

## Adoption notes

- **Wire the derivation at the existing decision points** — post-approval dispatch, advisory invocation, the verdict's gap check, the no-match fall-through. The facts already exist; attribution is plumbing, not new instrumentation.
- **Keep the enum closed.** The first unfitting case will invite a sixth class; its value is its closedness — extend by amendment, not at the keyboard.
- **State the padlock caveat in the consumer docs on day one**, not after the first over-trust incident.
- **Unrouted turns are demand evidence.** A `native` record means someone asked the platform for something it doesn't do — the highest-value catalog-roadmap input available, generated daily and otherwise discarded. Record it (a summary field on the native record); let admission policy decide against evidence rather than intuition. A record, not a mechanism — nothing auto-proposes.
- **Cost placement:** one attribution event per turn at route time, never per action — per-action attribution already lives in the approval surface and run records; repeating it re-bills the tightest budget in the system for no information.

## Metadata registrations

This APR introduces **no new component-metadata field**, consistent with APR-024: attribution classes and records are properties of **turns and sessions** (runtime state and audit artifacts), not DECLARE classification axes on components. The advisory class *reads* existing declarations (e.g. standalone eligibility); it does not add one. The one runtime-facing obligation is registered with APR-018 as R15 (§Runtime obligations).

## References

External sources referenced in this APR; see *Relationship to established patterns* for how each relates. The originating field experience — a draft ADR of a promptware framework in private development (2026-07-27) — is described in the Motivation and is not publicly linkable.

1. SLSA. *Supply-chain Levels for Software Artifacts — provenance and attestation model*. <https://slsa.dev/>
2. in-toto. *A framework to secure the integrity of software supply chains (attestations)*. <https://in-toto.io/>
3. C2PA. *Coalition for Content Provenance and Authenticity — Content Credentials*. <https://c2pa.org/>
4. Felt, A. P. et al. *Rethinking Connection Security Indicators*. SOUPS, 2016. <https://www.usenix.org/conference/soups2016/technical-sessions/presentation/porter-felt>
5. Schechter, S. et al. *The Emperor's New Security Indicators*. IEEE S&P, 2007. <https://ieeexplore.ieee.org/document/4223213>
6. European Parliament and Council. *Regulation (EU) 2024/1689 (Artificial Intelligence Act)* — Art. 50, Transparency obligations. 2024. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng>

## Change log

| Version | Date | Status | Change |
| --- | --- | --- | --- |
| 0.1.0 | 2026-07-27 | Draft | Initial draft published as APR-025, per proposal issue #41. Surfaced by adopter field experience (SpecOrigin draft ADR-019, 2026-07-27). Registers runtime obligation R15 with APR-018. |
