---
apr: 24
title: "A Coverage-Honest Verdict Principle for Promptware"
abstract: "Every verdict carries orthogonal severity (what was found) and coverage (what was examined), coverage computed from recorded gap facts over the check's input closure. A clean pass over an incomplete closure is inexpressible, and every gap names the action that would close it. Kills the vacuous pass."
status: Draft
class: architectural
version: 0.1.1
principals:
  - D. Maxios
generative-contributors:
  - "Claude Fable 5 (Anthropic)"
created: 2026-07-26
last-updated: 2026-07-26
audience: Architects of governance and eval machinery; authors of conformance checks, review gates, and CI fitness functions; harness builders whose runs consume verdicts downstream
supersedes: []
superseded-by: []
related:
  - APR-002
  - APR-003
  - APR-009
  - APR-010
  - APR-013
  - APR-017
  - APR-021
  - APR-023
tags:
  - verdicts
  - coverage
  - vacuous-pass
  - governance
  - evals
  - gaps
---

# APR-024 — A Coverage-Honest Verdict Principle for Promptware

> **Every verdict carries two orthogonal values — severity (`pass | fail`, what was found) and coverage (`complete | partial`, what was examined) — where coverage is computed from recorded gap facts over the check's input closure. A clean pass over an incomplete closure is inexpressible, and every gap names the action that would close it.**

*Injectable summary (for feeding to an LLM): [`digests/APR-024-coverage-honest-verdicts.md`](digests/APR-024-coverage-honest-verdicts.md). This full APR is canonical.*

## Motivation

The failure this principle kills is the **vacuous pass**: a check renders `pass` having examined little or nothing — *absence of checking rendered as positive evidence of checking*. Field experience supplies it in pure form: an instrumented framework run whose block-severity security gate printed `✓ — 1 gate(s) applied, 1 pass, 0 block` and exited 0 **without examining a single claim**, and whose audit found 31 of 93 CI fitness functions had *no measurement method at all* — 11 of them blocking severity — every one rendering as a silent pass. The run's own summary named the disease: the system *"characterises far more than it enforces, and its gates pass vacuously."*

The failure is promptware-specific in origin. Prompt-enforced rules and probabilistic checkers degrade not by crashing but by **narrowing silently** — a judged review that skimmed, a gate whose predicate was never wired to a measurement, an eval that ran on the subset that happened to be present. And it launders systematically: a degraded input ([APR-017](APR-017-graceful-degradation.md)), an AI-substituted review ([APR-009](APR-009-human-in-the-loop.md)), a reduced-effort run — each individually recorded — flow into a downstream verdict that reports the same clean `pass` as a full examination. The corpus marks degraded **artifacts** taint-style (APR-017), but defines no **verdict algebra**: the check *consuming* a marked artifact may still claim an unqualified pass, and [APR-010](APR-010-governance.md) / [APR-021](APR-021-evaluation.md) implicitly assume a check that *ran* is a check that *covered*.

Two independent derivations argue this is load-bearing, not incidental: the field framework arrived at `verdict × coverage` under incident pressure, and the corpus's own conformance-assessment skill independently shipped `det-clean (judgment pending)` — a verdict refusing to claim more coverage than it has — because nothing weaker was honest.

## The principle

> **Severity and coverage are orthogonal, and both are load-bearing. "Looked at half, found nothing" is not a severity statement — collapsing the two is what produces the vacuous pass. So: separate values, schema-enforced; coverage derived from recorded facts, never self-reported; gaps actionable, never decorative.**

| `severity` | `coverage` | Rendered | Meaning |
| --- | --- | --- | --- |
| `pass` | `complete` | **`pass`** | everything in scope examined; nothing above threshold |
| `pass` | `partial` | **`pass-with-gaps`** | nothing above threshold *in what was examined*; gaps enumerated |
| `fail` | any | **`fail`** | a finding above the blocking threshold |

Three rules make the table honest rather than cosmetic:

- **The inexpressibility rule.** A check MUST NOT emit `coverage: complete` while `gaps[]` is non-empty over its transitive input closure — and this is **enforced in schema or code, never in prose** (a prompt-enforced honesty rule is the failure mode recursing; composes [APR-003](APR-003-code-prompt-boundary.md)).
- **Gaps are facts from the closure-holder.** `gaps[]` entries are recorded events — an unsatisfied declared degradation, a substituted judgment, a declared sub-full effort, a budget-capped partial result, a fitness function with no measurement method — authored by the component that holds the plan/closure, **never self-reported by the checked component** (the no-self-adjudication corollary, APR-003 §5.1).
- **The actionability rule.** Every gap MUST name the action that would close it. `pass-with-gaps (3 gaps — schedule a12, a13, human-verify a15)` is actionable; a bare `pass-with-gaps` is an alarm nobody reads within a week — the vacuity failure in a new costume.

## Scope and applicability

### When this applies

- Any **verdict-emitting check** over promptware whose result is consumed downstream as assurance: governance checks ([APR-010](APR-010-governance.md)), eval and regression gates ([APR-021](APR-021-evaluation.md)), conformance assessments, review verdicts, CI fitness functions.

### When this does NOT apply

- **The severity threshold.** What separates `pass` from `fail` is per-scope policy and stays with [APR-010](APR-010-governance.md)/[APR-021](APR-021-evaluation.md); this APR adds an axis, it does not move the bar.
- **Point-in-time human judgment with no downstream consumer** — a reviewer's opinion read once by a human is not a verdict artifact.
- **Artifact-level degradation marking** — owned by [APR-017](APR-017-graceful-degradation.md); those marks are an *input feed* to `gaps[]`, not this APR's subject.

## Prescription

- A verdict-emitting check **MUST** emit `severity` and `coverage` as distinct values; rendering merges them per the table. Folding coverage into a severity enum (a single `pass | pass-with-gaps | fail` value) is non-conformant: it loses the name for "no findings *and* full coverage".
- `coverage: complete` with a non-empty `gaps[]` over the check's transitive input closure **MUST** be inexpressible — rejected by schema or code, not discouraged by prose.
- `gaps[]` **MUST** be recorded facts authored by the plan/closure-holder; the checked component **MUST NOT** self-report its own coverage (APR-003 §5.1).
- Every gap **MUST** name the action that would close it.
- A check with **no measurement method** for its predicate **MUST** register that as a gap; it **MUST NOT** render a clean pass. (This single rule converts every unmeasurable fitness function from a silent pass into a visible `pass-with-gaps`, with no per-check work.)
- Gaps **MUST propagate**: a promoted or derived artifact carries the gaps of the verdicts it passed through in its provenance ([APR-013](APR-013-artifact-graph.md) edges, APR-017 boundary marks), so a downstream consumer reads a gappy artifact *as* gappy — never as clean context. `pass-with-gaps` **SHOULD NOT** block promotion by default (blocking would push operators back to untracked side-channels); making it blocking is per-scope policy.
- Cross-graph **confidence aggregation MUST NOT substitute for coverage facts**: min-ing or multiplying local scores yields an uncalibrated number, and a number that is computed but never gates anything is the vacuity pattern itself. Confidence stays local and per-claim.
- Recorded sub-full effort and substituted judgment ([APR-009](APR-009-human-in-the-loop.md)) **MUST** appear as gaps in every verdict over the affected closure — a shallow pass is *visibly* shallow, which is a stronger guarantee than a prohibition the operator can route around.

## Governance and validation

The shared governance model — two-tier enforcement, audit-log binding, and change-via-ADR — is defined in [APR-010](APR-010-governance.md); the checks below are this APR's domain-specific additions.

A conformant platform checks, in review or CI:

- **Inexpressibility enforced mechanically** — the verdict schema (or emitting code) rejects `complete` + non-empty `gaps[]`; the rule is nowhere enforced only by prose (Tier 1).
- **Gaps actionable** — every gap entry carries a closing action (Tier 1 presence; Tier 2 judges usefulness).
- **No unmeasurable clean passes** — a scan of verdict-emitting checks finds none whose predicate lacks a measurement method yet renders `pass` (Tier 1).
- **Gap propagation** — promoted/derived artifacts carry upstream verdict gaps in provenance (Tier 1).
- **No severity folding, no confidence aggregation** — verdicts keep two fields; no cross-graph score stands in for coverage facts (Tier 2 review).

## What this principle is NOT

- **Not a severity policy.** It never says where `pass` ends and `fail` begins — APR-010/021 territory.
- **Not a numeric coverage metric.** `complete | partial` plus enumerated gap facts, not a percentage; a percentage without the facts is another computed-but-ungated number.
- **Not a blocking policy.** It makes gaps visible and propagated; whether they block is the adopter's per-scope call.
- **Not APR-017's artifact marking.** APR-017 marks what a *producer* emitted under degradation; this governs what a *checker* may claim — the marks feed the gaps.
- **Not a guarantee that findings are correct.** Coverage honesty says what was examined, not that the examination judged well — finding quality remains APR-021's eval discipline.

## Relationship to established patterns

| Pattern | What it shares with this APR | What this APR adds |
| --- | --- | --- |
| **Qualified audit opinions / scope limitations** (ISA 705) | An examiner who could not examine part of the scope may not issue a clean opinion | The qualification is machine-enforced (inexpressible, not discretionary), computed from recorded facts, and propagates with the examined artifact |
| **Code-coverage metrics** (Marick) | "Tests passed" is meaningless without "over what" | Generalized from executed-lines to a check's *transitive input closure*, with enumerated gaps instead of a percentage |
| **Assume-guarantee / partial verification** (formal methods) | A proof valid under assumptions carries its assumptions | The assumptions are runtime-recorded degradations and substitutions, not authored side-conditions |
| **Three-valued verdicts** (e.g. `skip`/`inconclusive` in test frameworks) | A result that is neither clean pass nor fail | Derived from two orthogonal axes rather than a third enum member — preserving the name for "no findings *and* full coverage" |
| **In-corpus:** [APR-017](APR-017-graceful-degradation.md), [APR-009](APR-009-human-in-the-loop.md), [APR-003](APR-003-code-prompt-boundary.md), [APR-010](APR-010-governance.md) | Boundary-marked degradation; recorded judgment substitution; no-self-adjudication; two-tier enforcement | The verdict-level algebra those mechanisms feed: marks and substitutions become gaps, gaps become qualified verdicts, and the qualification cannot be omitted |

The novel contribution is a **promptware-specific verdict algebra**: severity and coverage as orthogonal, schema-enforced values fed by the corpus's existing degradation and substitution records — so the vacuous pass, the signature failure of prompt-enforced governance, becomes structurally inexpressible rather than merely discouraged.

## Adoption notes

- **Start with the checks that gate promotion or block CI** — they are where a vacuous pass does the most downstream damage.
- **Wire the gap sources you already record.** APR-017 boundary marks, APR-009 substitution records, and effort declarations are existing facts; the first increment is plumbing them into verdicts, not new instrumentation.
- **Sweep for unmeasurable checks early.** The no-measurement-method rule finds them wholesale; expect the count to be embarrassing, and treat each as a gap until wired.
- **Expect `pass` to become rare — that is the point.** Any gap of any kind forces `pass-with-gaps`, re-basing what a clean verdict means for every downstream reader. The rejected alternative — scoping "complete" to whatever the current tier defines — makes `pass` mean different things depending on a dial invisible from the verdict.
- **Defend against alarm fatigue with the actionability rule**, not by softening the third verdict.

## Metadata registrations

This APR introduces **no new component-metadata field**, consistent with APR-015/016/017: `severity`, `coverage`, and `gaps[]` are fields of **verdict and run artifacts** (governance outputs, eval reports, run locks), not DECLARE classification axes on components. Their shapes belong to the platform's verdict/run schemas, governed by this APR's inexpressibility rule.

## References

External sources referenced in this APR; see *Relationship to established patterns* for how each relates. The originating field experience — the SpecOrigin ADRs below, grounded in an instrumented run — is described in the Motivation.

1. Maxios, D. *SpecOrigin — ADR-014: Dependency modes, recorded degradation, and coverage-carrying verdicts*. SpecOrigin framework, 2026. *(Currently a private repository; publication is planned.)*
2. Maxios, D. *SpecOrigin — ADR-015: Planning effort*. SpecOrigin framework, 2026. *(Currently a private repository; publication is planned.)*
3. Maxios, D. *SpecOrigin — ADR-016: Scheduled verification checkpoints*. SpecOrigin framework, 2026. *(Currently a private repository; publication is planned.)*
4. IAASB. *ISA 705 (Revised) — Modifications to the Opinion in the Independent Auditor's Report*. <https://www.iaasb.org/publications/international-standard-auditing-isa-705-revised-modifications-opinion-independent-auditor-s-report>
5. Marick, B. *How to Misuse Code Coverage*. 1999. <http://www.exampler.com/testing-com/writings/coverage.pdf>
6. Jones, C. B. *Tentative Steps Toward a Development Method for Interfering Programs* (assume-guarantee reasoning). ACM TOPLAS 5(4), 1983. <https://doi.org/10.1145/69575.69577>

## Change log

| Version | Date | Status | Change |
| --- | --- | --- | --- |
| 0.1.1 | 2026-07-30 | Draft | Added SpecOrigin ADR reference(s) — the adopter field source, named per the principal's direction (currently a private repository; publication is planned). No semantic change. |
| 0.1.0 | 2026-07-26 | Draft | Initial draft published as APR-024, per proposal issue #34. Surfaced by adopter field experience (SpecOrigin draft ADRs 014/015/016, 2026-07-26); independently re-derived by the apr-conformance reference skill. |
