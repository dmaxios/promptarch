# APR-023 — Declared Structural Rationale — Digest

> **Generated digest of [APR-023 — A Declared Structural-Rationale Principle for Promptware](../APR-023-structural-rationale.md) v0.1.0.** The full APR is authoritative — read it for motivation, prior art, and worked detail. Do not edit by hand.

**Abstract.** Behavior-shaping structure declares why it exists: compensatory structure pins the capability baseline it offsets and is retired by re-benchmarking; coordinating structure names its human/organizational reason and is never loosened on capability grounds. Undeclared defaults to coordinating.

**Principle.** Declare the rationale; let the rationale drive the lifecycle. Compensatory structure is designed for removal (pinned baseline, enrolled in re-benchmarking); coordinating structure is designed to endure (exempt from capability-relative loosening). What is undeclared cannot be removed on capability grounds.

## Rationale classes

- **`compensatory`** — offsets a model capability gap. MUST pin `calibrated_against` (model baseline + eval evidence that established the gap). Enrolled in APR-008's bidirectional re-validation; retiring it is a version-bumped, recorded change. Missing baseline ⇒ treated as undeclared (unfalsifiable compensation).
- **`coordinating`** — serves humans/organizations (alignment, traceability, audit, blast-radius containment), independent of model competence. MUST name the coordinating need in the component's spec; MUST NOT be removed or loosened on model-capability grounds — changes only when its coordinating reason changes.
- **`mixed`** — both at once (e.g. a spec gate that aligns humans and compensates intent inference). MUST carry both declarations. Coordinating dominates removal: the element stays; only the compensatory share is re-benchmarkable.
- **Undeclared** — fail-safe default: treated as coordinating for removal (MUST NOT be removed on capability grounds), flagged for classification. Mirrors APR-009's unknown-reversibility → irreversible rule.

## Prescription

- Gating/blocking/forcing structure (approval gates, mandatory phases, forced loops) MUST declare `rationale`; other behavior-shaping structure (personas, verbosity conventions, decomposition defaults) SHOULD.
- On a model change, compensatory structure SHOULD be re-benchmarked (APR-008); retirement/loosening MUST be version-bumped and recorded — never silent drift.
- The agent MUST NOT self-assess a rationale at runtime — a probabilistic model deciding whether structure constraining it is still needed is an APR-003 violation. `rationale` is declared metadata, assigned by a principal.

## Governance checks

`compensatory`/`mixed` carries `calibrated_against` (else flagged as undeclared) · no removal/loosening of coordinating or undeclared structure citing model capability · compensatory retirement is version-bumped with re-benchmark evidence recorded (APR-008 gate) · gating structure without a declared `rationale` flagged for classification.

## Metadata (registry, per APR-014)

`rationale: compensatory | coordinating | mixed` (core.classification) · `calibrated_against: { model, evidence }` (core.evaluation, required when compensatory/mixed).

## Scope limits — do NOT misapply

Not a framework verdict (classifies elements, not products) · not a license to strip structure (default protects: undeclared/coordinating never removed on capability grounds) · not a recalibration mechanism (APR-008 owns the re-benchmark event; this determines eligibility) · not a modification of APR-009 (blast-radius/reversibility placement stays capability-invariant — the canonical coordinating case) · not a claim the binary is always crisp (`mixed` + coordinating-dominates-removal).

---
*Source: [APR-023 — A Declared Structural-Rationale Principle for Promptware](../APR-023-structural-rationale.md) v0.1.0 · regenerate this digest whenever the source changes.*
