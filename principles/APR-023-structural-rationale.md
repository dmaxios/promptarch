---
apr: 23
title: "A Declared Structural-Rationale Principle for Promptware"
abstract: "Behavior-shaping structure declares why it exists: compensatory structure pins the capability baseline it offsets and is retired by re-benchmarking; coordinating structure names its human/organizational reason and is never loosened on capability grounds. Undeclared defaults to coordinating."
status: Draft
class: architectural
version: 0.1.0
principals:
  - D. Maxios
generative-contributors:
  - "Claude Fable 5 (Anthropic)"
  - "Claude Sonnet 5 (Anthropic)"
created: 2026-07-25
last-updated: 2026-07-25
audience: Architects of agentic platforms and framework authors deciding which structure survives model upgrades; teams executing model migrations under APR-008
supersedes: []
superseded-by: []
related:
  - APR-003
  - APR-006
  - APR-008
  - APR-009
  - APR-014
tags:
  - structural-rationale
  - compensatory
  - coordinating
  - model-capability
  - calibration
  - lifecycle
---

# APR-023 — A Declared Structural-Rationale Principle for Promptware

> **Every behavior-shaping structural element declares *why it exists* — compensatory (it offsets a declared model-capability baseline, and is retirable by re-benchmarking) or coordinating (it serves humans and organizations, and is never loosened on capability grounds) — and undeclared or mixed structure defaults to coordinating for removal purposes.**

*Injectable summary (for feeding to an LLM): [`digests/APR-023-structural-rationale.md`](digests/APR-023-structural-rationale.md). This full APR is canonical.*

## Motivation

As models grow more capable, promptware systems accumulate two symmetric failure modes, and without this principle neither has an owner:

- **Fossilized compensation.** Forced planning loops, role personas, mandatory decomposition, and verbose specification built for a weaker model persist as dead weight, because nothing marks them as compensating a capability gap — so nothing makes them retirable. Compensation without a declared baseline is unfalsifiable: nobody can say which model change should have retired it.
- **Bitter-lesson over-correction.** Capability growth is used as an argument to strip *coordinating* structure — traceability, review gates, audit history — that never depended on model competence. A stronger model does not change what a team must agree on before building, or what an auditor needs afterward.

The two failure modes share one root cause: **the rationale for a structural element is undeclared**, so load-bearing coordination and fossilized compensation are indistinguishable. Both then persist (dead weight), or both get stripped (the over-correction). The public "do we still need frameworks" debate conflates exactly these two kinds of structure — see the [model-capability-growth study](../docs/studies/2026-07-25-model-capability-growth.md), which surfaced this principle.

[APR-008](APR-008-artifact-lifecycle.md) (v0.2.0) already makes model-migration re-validation bidirectional — but only for the slice of structure with a number attached (`min_eval_score` thresholds, eval-placed approval tiers). The rationale of everything else — phases, loops, personas, decomposition mandates — is undeclared, so neither deliberate retirement nor principled defense is possible. This APR generalizes that clause from *eval-calibrated gates* to *all behavior-shaping structure*, by making the rationale itself a declared, machine-readable property.

## The principle

> **Declare the rationale; let the rationale drive the lifecycle.** A structural element whose rationale is *compensatory* pins the capability baseline it offsets and is enrolled in re-benchmarking — designed for removal. A structural element whose rationale is *coordinating* names the human or organizational need it serves and is exempt from capability-relative loosening — designed to endure. What is undeclared cannot be removed on capability grounds.

The classification discipline is itself capability-invariant: better models never erode the *need to know why structure exists* — they only migrate content from the compensatory class into retirement.

## Scope and applicability

### When this applies

- **Behavior-shaping structural elements** of promptware systems and frameworks: gates, mandatory phases and loops, forced planning or verification steps, role personas, decomposition topology requirements, verbosity/specification requirements — anything whose presence shapes agent behavior and could in principle be removed.
- Any platform executing **model migrations under APR-008**, which needs to know what is re-benchmarkable and what is off-limits.

### When this does NOT apply

- **Judging specific public frameworks.** This is a mechanism for declaring rationale, not a catalogue of verdicts on Superpowers, SpecKit, or anyone else (the mechanism-vs-catalogue trap).
- **Runtime mechanism** (schedulers, dispatch loops, retry/backoff) — out of corpus scope per the [harness-coverage study](../docs/studies/harness-coverage.md).
- **[APR-009](APR-009-human-in-the-loop.md)'s oversight placement.** Placement by reversibility and blast radius is capability-invariant *by construction* — a stronger model does not shrink the blast radius of an irreversible action. APR-009's structure is *cited here* as the canonical coordinating case; it is not modified, and it does not need a declared rationale to be protected (it is protected by APR-009 itself).
- **The recalibration event and the declaration mechanism.** APR-008 owns *when and how* compensatory structure is re-benchmarked; [APR-014](APR-014-declare.md) owns *how* classifications are declared. This APR adds one classification axis and the lifecycle rule attached to it — it redefines neither.

## The rationale classes

| Rationale | Why the structure exists | Declared evidence | Lifecycle treatment |
| --- | --- | --- | --- |
| `compensatory` | Offsets a model capability gap — the structure would be unnecessary if the model were reliably capable of the behavior unaided | **MUST** pin `calibrated_against`: the model baseline it compensates for, plus the eval evidence that established the gap | Enrolled in APR-008's bidirectional re-validation; **designed for removal** — retiring it is a version-bumped, recorded change |
| `coordinating` | Serves humans and organizations — multi-party alignment, traceability, audit, review, blast-radius containment — independent of model competence | **MUST** name the coordinating need in the component's specification (ASPECT prose; a human-facing justification, not a registry field) | **Exempt from capability-relative loosening** — changed only for coordinating reasons (the humans or the organization changed), never because the model improved |
| `mixed` | Both at once — e.g. a specification gate that aligns humans *and* compensates for unreliable intent inference | **MUST** carry both: `calibrated_against` for the compensatory share, the named coordinating need for the rest | Treated as **coordinating for removal** (the element stays); the compensatory *share* is re-benchmarkable — e.g. the required verbosity may shrink while the gate itself remains |

**Undeclared** structure gets the fail-safe default: it is treated as coordinating *for removal purposes* — it MUST NOT be removed or loosened on capability grounds — and is flagged for classification. This mirrors APR-009's unknown-reversibility → irreversible rule: when a safety-relevant property is unknown, assume the conservative value and surface the gap, rather than letting the ambiguity license the risky action.

## Prescription

- A behavior-shaping structural element that gates, blocks, or forces behavior (approval gates, mandatory phases, forced loops) **MUST** declare its `rationale`; other behavior-shaping structure (personas, verbosity conventions, decomposition defaults) **SHOULD**.
- A `compensatory` or `mixed` declaration **MUST** pin `calibrated_against` — the model baseline the structure compensates for and the eval evidence that established the gap. Compensatory structure without a baseline is flagged: unfalsifiable compensation is treated as undeclared.
- `compensatory` structure (and the compensatory share of `mixed`) is enrolled in [APR-008](APR-008-artifact-lifecycle.md)'s bidirectional re-validation: on a model change it **SHOULD** be re-benchmarked, and retiring or loosening it **MUST** be a version-bumped, recorded change — never silent drift.
- `coordinating` structure **MUST** name the coordinating need it serves in the component's specification, and **MUST NOT** be removed or loosened on model-capability grounds. It changes only when its coordinating reason changes.
- `mixed` structure is treated as coordinating for removal; only its declared compensatory share is re-benchmarkable.
- Undeclared structure **MUST NOT** be removed or loosened on capability grounds until classified; a conformant platform flags it for classification rather than resolving the ambiguity at removal time.
- The agent **MUST NOT** self-assess a rationale at runtime — letting a probabilistic model decide whether structure constraining it is still necessary is an [APR-003](APR-003-code-prompt-boundary.md) violation, and the conflict of interest is obvious. `rationale` is declared metadata, assigned by a principal.

## Governance and validation

The shared governance model — two-tier enforcement, audit-log binding, and change-via-ADR — is defined in [APR-010](APR-010-governance.md); the checks below are this APR's domain-specific additions.

A conformant platform checks, in review or CI:

- **Baseline present** — every `compensatory`/`mixed` declaration carries `calibrated_against`; missing baseline is flagged as undeclared.
- **No capability-grounds loosening of coordinating structure** — a change that removes or weakens `coordinating` (or undeclared) structure citing model capability as the reason is flagged.
- **Retirement discipline** — retiring compensatory structure is a version-bumped change with the re-benchmark evidence recorded (via APR-008's migration gate), not a silent deletion.
- **Classification coverage** — gating/blocking/forcing structure without a declared `rationale` is flagged for classification.

## What this principle is NOT

- **Not a framework verdict.** It classifies *elements*, not products; applying it to a specific framework is an adopter's exercise, not corpus content.
- **Not a license to strip structure.** Its default runs the other way: what is undeclared or coordinating cannot be removed on capability grounds. It makes retirement *deliberate*, not *easy*.
- **Not a recalibration mechanism.** APR-008 owns the re-benchmark event; this APR determines what is *eligible* for it.
- **Not a modification of APR-009.** Blast-radius/reversibility placement stays capability-invariant; this APR cites it as the canonical coordinating case.
- **Not a claim that the binary is always crisp.** Real structure is often mixed; the `mixed` class plus the coordinating-dominates-removal rule handles it without forcing a false dichotomy.

## Relationship to established patterns

| Pattern | What it shares with this APR | What this APR adds |
| --- | --- | --- |
| **Architecture Decision Records** (Nygard) | Recording *why* a structural choice was made | The why is machine-readable metadata that *drives lifecycle* (retirable vs. protected), not an archival note |
| **Feature-flag hygiene / expiry-dated flags** (Fowler, Hodgson) | Structure declared temporary, with an explicit retirement condition, against flag debt | The retirement condition is a *model-capability baseline*, checked at APR-008 migration events rather than a calendar date |
| **Scaffolding and fading** (Wood, Bruner & Ross) | Support explicitly designed for removal as capability grows | Applies the scaffold/fade distinction to promptware structure, with the non-fading (coordinating) class explicitly protected |
| **Technical-debt registers** | Making invisible structural debt visible and reviewable | Debt here is *compensation debt*, discovered by re-benchmarking rather than self-report |
| **The compensatory/coordinating distinction** ([model-capability-growth study](../docs/studies/2026-07-25-model-capability-growth.md); Bowne-Anderson; Zunic) | The analytical lens: capability growth *relocates* engineering rather than eliminating it | Turns the lens into a declared, enforceable classification with a fail-safe default |
| **In-corpus:** [APR-014](APR-014-declare.md), [APR-008](APR-008-artifact-lifecycle.md), [APR-009](APR-009-human-in-the-loop.md), [APR-017](APR-017-graceful-degradation.md) | DECLARE's declared-classification contract; APR-008's bidirectional re-validation; APR-009's fail-safe unknown-default and track-record-weighted rigor; APR-017's precedent of unifying a cross-APR concern | One orthogonal classification axis (`rationale`) wired into the existing declaration, migration, and fail-safe machinery |

The novel contribution is making **structural rationale a first-class, declared, lifecycle-driving property** of promptware — so capability growth retires compensation deliberately, and coordination survives it by design rather than by argument.

## Adoption notes

- **Start with the gates.** Classify the structure that blocks or forces behavior first (approval gates, mandatory phases, forced loops) — that is where the MUST bites and where both failure modes are most expensive.
- **Run a compensation census at the next model migration.** The APR-008 re-benchmark event is the natural moment to classify existing structure: whatever the new model clears with wide margin is a compensatory candidate; whatever a human or auditor would miss is coordinating.
- **Expect `mixed` to be common.** A spec-first gate usually both aligns humans and compensates inference. Declare both; let the verbosity shrink while the gate endures.
- **Resist retroactive rationalization.** When classifying old structure, the honest answer to "why does this exist?" is sometimes "we no longer know" — that is `undeclared` (protected but flagged), not a guessed `coordinating`.

## Metadata registrations

Component-metadata fields this APR owns, registered per [APR-014 §The metadata registry](APR-014-declare.md) in [`registries/component-metadata.yaml`](../registries/component-metadata.yaml):

| Field | Path | Type | Values | Status |
| --- | --- | --- | --- | --- |
| `rationale` | core.classification | enum | `compensatory, coordinating, mixed` | active |
| `calibrated_against` | core.evaluation | object | `{ model, evidence }` — the model baseline compensated for, and the eval evidence (ref-list) that established the gap | active |

## References

External sources referenced in this APR; see *Relationship to established patterns* for how each relates.

1. Bowne-Anderson, H. *Are better models making agent engineering obsolete?* 2026. <https://hugobowne.substack.com/p/are-better-models-making-agent-engineering>
2. Zunic, N. *The Bitter Lesson of Agent Frameworks*. browser-use, 2026. <https://browser-use.com/posts/bitter-lesson-agent-frameworks>
3. Sutton, R. *The Bitter Lesson*. 2019. <http://www.incompleteideas.net/IncIdeas/BitterLesson.html>
4. Nygard, M. *Documenting Architecture Decisions*. 2011. <https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions>
5. Hodgson, P. *Feature Toggles (aka Feature Flags)*. martinfowler.com, 2017. <https://martinfowler.com/articles/feature-toggles.html>
6. Wood, D., Bruner, J. S., & Ross, G. *The Role of Tutoring in Problem Solving*. Journal of Child Psychology and Psychiatry, 17(2), 1976. <https://doi.org/10.1111/j.1469-7610.1976.tb00381.x>

## Change log

| Version | Date | Status | Change |
| --- | --- | --- | --- |
| 0.1.0 | 2026-07-25 | Draft | Initial draft published as APR-023, per proposal issue #29. Generalizes APR-008 v0.2.0's bidirectional-migration clause to all behavior-shaping structure. Surfaced by the [model-capability-growth study](../docs/studies/2026-07-25-model-capability-growth.md). |
