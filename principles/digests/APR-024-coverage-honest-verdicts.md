# APR-024 — Coverage-Honest Verdicts — Digest

> **Generated digest of [APR-024 — A Coverage-Honest Verdict Principle for Promptware](../APR-024-coverage-honest-verdicts.md) v0.1.0.** The full APR is authoritative — read it for motivation, prior art, and worked detail. Do not edit by hand.

**Abstract.** Every verdict carries orthogonal severity (what was found) and coverage (what was examined), coverage computed from recorded gap facts over the check's input closure. A clean pass over an incomplete closure is inexpressible, and every gap names the action that would close it. Kills the vacuous pass.

**Principle.** Severity and coverage are orthogonal and both load-bearing — "looked at half, found nothing" is not a severity statement. Separate values, schema-enforced; coverage derived from recorded facts, never self-reported; gaps actionable, never decorative.

## The verdict table

| `severity` | `coverage` | Rendered | Meaning |
| --- | --- | --- | --- |
| `pass` | `complete` | **`pass`** | everything in scope examined; nothing above threshold |
| `pass` | `partial` | **`pass-with-gaps`** | clean *in what was examined*; gaps enumerated |
| `fail` | any | **`fail`** | a finding above the blocking threshold |

## Normative rules

- Verdict-emitting checks MUST emit `severity` and `coverage` as **distinct values**; folding them into one enum is non-conformant (loses the name for "no findings AND full coverage").
- **Inexpressibility**: `coverage: complete` + non-empty `gaps[]` over the transitive input closure MUST be rejected by **schema or code, never prose** (a prompt-enforced honesty rule is the failure recursing — APR-003).
- `gaps[]` are **recorded facts from the plan/closure-holder** (unsatisfied declared degradation, substituted judgment, sub-full effort, budget-capped partial result, check with no measurement method); the checked component MUST NOT self-report coverage (APR-003 §5.1 no-self-adjudication).
- **Actionability**: every gap MUST name the action that would close it — a bare `pass-with-gaps` is an alarm nobody reads.
- A check with **no measurement method** MUST register that as a gap, never render a clean pass.
- **Gaps propagate**: promoted/derived artifacts carry upstream verdict gaps in provenance (APR-013, APR-017 marks), so downstream reads gappy-as-gappy. `pass-with-gaps` SHOULD NOT block promotion by default; blocking is per-scope policy.
- **No confidence aggregation** in lieu of coverage facts — an uncalibrated computed-but-ungated number is the vacuity pattern. Confidence stays local, per-claim.
- Recorded sub-full effort and substituted judgment (APR-009) MUST surface as gaps — a shallow pass is *visibly* shallow (stronger than routable prohibition).

## Governance checks

Inexpressibility enforced mechanically (schema/code rejects complete+gaps) · every gap carries a closing action · no unmeasurable check renders `pass` · gaps propagate into promoted artifacts' provenance · verdicts keep two fields, no cross-graph score substitutes for coverage facts.

## Scope limits — do NOT misapply

Not a severity policy (the pass/fail bar stays with APR-010/021) · not a numeric coverage metric (enumerated facts, not a percentage) · not a blocking policy (visibility + propagation; blocking is the adopter's) · not APR-017's artifact marking (those marks *feed* the gaps; this governs what a *checker* may claim) · not a guarantee findings are correct (coverage honesty ≠ finding quality).

---
*Source: [APR-024 — A Coverage-Honest Verdict Principle for Promptware](../APR-024-coverage-honest-verdicts.md) v0.1.0 · regenerate this digest whenever the source changes.*
