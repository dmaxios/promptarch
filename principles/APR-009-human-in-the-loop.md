---
apr: 9
title: "A Human-in-the-Loop Oversight-Placement Principle for Promptware"
abstract: "Place human oversight by reversibility and blast radius: irreversible/high-blast actions get plan-and-approve (before); reversible/low-blast get fire-and-judge (after). Unknown defaults to irreversible; safety floors are never sampled away; long runs batch questions to declared checkpoints."
status: Draft
class: architectural
version: 0.2.1
principals:
  - D. Maxios
generative-contributors:
  - "Claude Opus 4.8 (Anthropic; 1M context)"
  - "Claude Fable 5 (Anthropic)"
  - "Claude Opus 5 (Anthropic; 1M context; via SpecOrigin field ADRs 014-016)"
created: 2026-05-31
last-updated: 2026-07-26
audience: Architects of agentic systems that take consequential actions; framework authors building approval/review UX; teams in regulated or high-blast-radius domains
supersedes: []
superseded-by: []
related:
  - APR-001
  - APR-003
  - APR-005
  - APR-006
  - APR-017
tags:
  - human-in-the-loop
  - oversight
  - approval
  - autonomy
  - reversibility
---

# APR-009 — A Human-in-the-Loop Oversight-Placement Principle for Promptware

> **Human oversight is placed by reversibility and blast radius: irreversible or high-blast actions require *plan-and-approve* — a human gate before execution; reversible, low-blast actions use *fire-and-judge* — human review after. Reversibility selects the mode; blast radius tunes the rigor.**

*Injectable summary (for feeding to an LLM): [`digests/APR-009-human-in-the-loop.md`](digests/APR-009-human-in-the-loop.md). This full APR is canonical.*

## Motivation

Agentic promptware takes consequential actions — writing to shared state, calling external systems, applying code changes, spending money. [APR-001 ASPECT](APR-001-aspect.md) declares autonomy *levels* (L0–L5), and [APR-005](APR-005-trust-boundaries.md) / [APR-006](APR-006-composition-topology.md) require *escalation* to a human — but nothing defines **where the human gate sits relative to the action**. Without that, oversight fails two ways:

- **Over-gating** → the human approves everything, rubber-stamps, and real oversight evaporates.
- **Under-gating** → high-blast, irreversible actions execute silently.
- And the gate, when present, is **ad hoc** — no contract for what the human sees, decides, or can undo, and placement that doesn't track risk (a reversible draft and an irreversible deletion treated the same).

## The principle

> **An action's oversight mode is set by whether it can be undone; how heavy that oversight is, is set by how much it affects.**

- **Reversibility selects the mode.** Irreversible → **plan-and-approve** (gate *before*). Reversible → **fire-and-judge** (review *after*) — you can only judge-after what you can undo.
- **Blast radius tunes the rigor** — sampling rate, notification, rollback speed, batching — not the mode.

## Scope and applicability

### When this applies

- Wherever an agent takes actions a human should oversee: consequential, irreversible, safety-critical, or externally visible.

### When this does NOT apply

- Pure-advisory / read-only agents whose output a human consumes before any action (the human is already the actor).
- It is an *oversight-placement* principle, **not** a UX specification or an approval-workflow engine — it says where the gate sits and what it must carry, not how the UI works.

## The two modes

- **Plan-and-approve** (a-priori control). The agent presents its intended action; a human approves, rejects, or modifies it **before** execution. The action does not run until approved.
- **Fire-and-judge** (a-posteriori control). The agent executes; the action is captured and a human reviews it **after**. Valid only where the action is **reversible**, so a bad outcome can be rolled back.

## The placement rule

| | Reversible | Irreversible |
|---|---|---|
| **Low blast** | fire-and-judge (light: sampled) | plan-and-approve (lightweight / batchable) |
| **High blast** | fire-and-judge (heavy: notify, fast rollback, high sampling) | plan-and-approve (individual, mandatory) |

**Reversibility picks the column → the mode; blast radius picks the row → the rigor.** This resolves the mixed cells cleanly: a reversible-but-high-blast bulk edit is still fire-and-judge, but with immediate notification and easy rollback; an irreversible-but-low-blast single email is still plan-and-approve, but may be lightweight or batched.

## Reversibility is declared, not guessed

- Reversibility is **declared metadata** on the action/tool (like blast radius in an ASPECT Autonomy Profile), recording whether and how its effects can be undone.
- The agent **MUST NOT** self-assess reversibility ad hoc — letting a probabilistic model decide a safety property is an [APR-003](APR-003-code-prompt-boundary.md) violation; reversibility is deterministic metadata, not a model guess.
- **When reversibility is unknown or unclear, default to irreversible → plan-and-approve** (fail-safe, per [APR-005](APR-005-trust-boundaries.md)).

## What each mode must carry

**Plan-and-approve** — before execution, the human **MUST** be shown:

- the **intent** (what and why);
- the **concrete action/diff preview** — the *actual* change, not a model-written summary (the key anti-rubber-stamp rule);
- the **predicted blast radius**;
- the **rollback plan**, or an explicit "irreversible — no rollback" flag.

The decision (approve / reject / modify) is recorded with the **approver's identity**.

**Fire-and-judge** — it **MUST**:

- **capture** the action, its inputs, and outputs for review;
- record the **judgment** (accept / flag / correct) with the **reviewer's identity**;
- **feed back** — corrections become eval cases ([APR-008](APR-008-artifact-lifecycle.md) / OBSERVE) and may become patterns ([APR-007](APR-007-pattern-mechanism.md)), so judgments improve the system rather than being discarded.

## When judgment happens: scheduled checkpoints

The placement rule says whether oversight sits *before* or *after* an action. For **long-running autonomous work** there is a third question the mode alone does not answer: *when* does the human interaction happen? Field experience gives the failure its sharpest form: a doctrine whose only permitted responses to "a human must decide something mid-run" are **silently skip** or **halt indefinitely** will produce one or the other — and one observed run spent 70% of its wall-clock span blocked on a single unbatched mid-run question.

- **Oversight interaction points in a long-running run MUST be scheduled at plan time** and visible in what the human approves — the operator knows *before approving* where they will be asked. Execution stays non-interactive *between* checkpoints.
- **Questions arising between checkpoints MUST be deferred and batched to the next checkpoint** — never surfaced ad hoc at unpredictable moments, and never silently dropped. Batching converts an unpredictable blocking interruption into one predictable interaction answerable in minutes.
- A question whose action **cannot proceed unanswered** is not a checkpoint case — it follows [APR-017](APR-017-graceful-degradation.md)'s escalation discipline (a blocking wait with a declared timeout whose expiry fails closed). Checkpoints give *deferrable* decisions a scheduled home; they do not replace escalation for blocking ones.
- Plan-and-approve **MAY be staged at phase boundaries**: approve the early phases, inspect what returns, then decide whether the later phases are warranted. Staging keeps a long plan's approval honest without shortening the plan.

**Judging MAY be AI-assisted, under two hard conditions.** An unattended run may substitute an AI reviewer at a checkpoint, but (1) the substitute **MUST be an isolated dispatch that is not the producer** of the artifact under review — a model answering its own deferred questions is self-judging, the same violation as self-assessed reversibility — and (2) the substitution **MUST be recorded** as a judgment gap in the run record, and **MUST NOT count as the human review** anywhere the safety floor requires one. Automation and honesty stop competing: the unattended run is possible, and its record tells the truth about having been unattended.

## Avoiding approval fatigue

Fatigue is real: a human who must approve everything stops reading and rubber-stamps, which is *worse* than no gate. The answer is **tunable levers under a hard safety floor** — cut fatigue by *not gating trivia*, never by lightening the gates that matter:

- **Thresholds** — do not gate actions below a declared blast-radius/cost threshold.
- **Risk-weighted sampling** — fire-and-judge reviews a sample, with the rate rising with blast radius and falling with track record.
- **Batching** — homogeneous low-stakes actions may be approved or judged as a batch.
- **Anchoring** — *the anchor does the deciding*: a long plan presented with every item pre-checked is approved as-is nearly every time, so the concrete-diff rule alone does not prevent rubber-stamping. The approval surface SHOULD present the **minimal default selection** with the additions *offered* (expandable), not the maximal set pre-checked — same information, opposite default. Deselection MUST be **dependency-aware**: unchecking an item reports its cascade ("also drops X, Y — you lose your target"), so the human never approves a plan that cannot run.

**The hard floor:** safety-critical or irreversible-high-blast actions are **NEVER** sampled-out or batched-away — they always get individual plan-and-approve.

## Prescription

- Every overseeable action **MUST** have a declared **reversibility**; unknown **MUST** default to irreversible.
- Oversight mode **MUST** be set by reversibility (irreversible → plan-and-approve; reversible → fire-and-judge); blast radius sets the rigor, not the mode.
- Plan-and-approve **MUST** show the human the concrete diff, intent, blast radius, and rollback plan, and record the approver's identity, **before** execution.
- Fire-and-judge **MUST** capture the action, record the judgment with reviewer identity, and feed corrections back to evals/patterns; it **MUST** be used only for reversible actions.
- The agent **MUST NOT** self-assess reversibility (APR-003); it is declared metadata.
- Fatigue reduction (thresholds, sampling, batching, anchoring) **MUST NOT** apply to safety-critical or irreversible-high-blast actions, which always receive individual approval.
- A long-running run **MUST** declare its oversight checkpoints at plan time; deferrable questions arising between checkpoints **MUST** batch to the next one; blocking questions follow APR-017 escalation. Ad hoc mid-run prompts and silently dropped questions are both non-conformant.
- Approval surfaces **SHOULD** present the minimal plan as the default selection with additions offered; deselection **MUST** be dependency-aware.
- An AI-substituted review **MUST** be an isolated, non-producer dispatch, **MUST** be recorded as a substitution in the run record, and **MUST NOT** satisfy the safety floor.

## Governance and validation

The shared governance model — two-tier enforcement, audit-log binding, and change-via-ADR — is defined in [APR-010](APR-010-governance.md); the checks below are this APR's domain-specific additions.

A conformant platform checks, in review or CI:

- **Reversibility declared** — every overseeable action/tool carries it; unknown defaults to irreversible.
- **Mode matches reversibility** — irreversible actions are gated before; reversible ones are reviewed after.
- **Approval payload** — plan-and-approve records show the concrete diff and approver identity, not a summary.
- **Judgment feedback** — fire-and-judge corrections are captured and routed to evals/patterns.
- **Safety floor intact** — no sampling/batching/threshold/anchoring path lets a safety-critical or irreversible-high-blast action skip individual approval.
- **Checkpoints declared** — long-running runs show their oversight checkpoints in the approved plan; traces contain no unscheduled mid-run prompts and no dropped deferred questions.
- **Substituted judgment recorded** — every AI-substituted review appears in the run record as a substitution, from an isolated non-producer dispatch, and never stands in where the safety floor requires a human.

## What this principle is NOT

- **Not a UX or workflow-engine spec.** It places the gate and defines its payload; the approval UI and routing are the platform's.
- **Not the autonomy-level definition.** ASPECT defines the levels; this defines which oversight *mode* a level selects.
- **Not a guarantee against human error.** It ensures the human is *informed and correctly placed*, not that they decide well.
- **Not access control or authn.** Who *may* approve is the platform's authorization concern; this governs *when and what* is overseen.
- **Not applicable to read-only/advisory agents** — there, the human is already the actor.

## Relationship to established patterns

| Pattern | What it shares with this APR | What this APR adds |
|---|---|---|
| **Four-eyes / maker–checker** | Approve-before for high-consequence actions | Mode chosen by reversibility; a concrete-diff payload; a fatigue discipline |
| **One-way vs. two-way doors** (Bezos) | Reversibility as the key decision axis | Maps reversibility directly onto oversight *placement* (before vs. after) |
| **Change-approval / deploy gates** (CI/CD) | Human gate before a consequential change | Generalized to agent actions, with risk-tuned rigor and post-hoc judging |
| **RLHF / human feedback** (Christiano et al.) | Humans judging machine outputs | Fire-and-judge feeds back into evals/patterns at *operation* time, not training |
| **Regulatory human-oversight mandates** (EU AI Act Art. 14; NIST AI RMF) | Required human oversight of AI | A concrete, placement-based mechanism that *implements* such oversight |

The novel contribution is a **promptware-specific oversight-placement principle**: two modes selected by reversibility, rigor tuned by blast radius, with declared (not guessed) reversibility, a concrete-diff approval payload, a judgment-feedback loop, and a fatigue discipline bounded by a hard safety floor — composing with ASPECT autonomy levels, APR-003 (the gate as a deterministic check), APR-005 (fail-safe escalation), and APR-006 (escalation up the graph).

## Metadata registrations

Component-metadata fields this APR owns, registered per [APR-014 §The metadata registry](APR-014-declare.md) in [`registries/component-metadata.yaml`](../registries/component-metadata.yaml):

| Field | Path | Type | Values | Status |
|---|---|---|---|---|
| `max_autonomy_level` | core.classification | enum | `L1, L2, L3, L4, L5` | active |
| `max_blast_radius` | core.classification | enum | `local-only, project-scoped, cross-project, external` | active |
| `escalation_triggers` | core.composition | list | — | active |
| `escalation_path` | core.composition | string | — | active |

## References

External sources referenced in this APR; see *Relationship to established patterns* for how each relates.

1. European Parliament and Council. *Regulation (EU) 2024/1689 (Artificial Intelligence Act)* — Art. 14, Human oversight. 2024. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng>
2. NIST. *AI Risk Management Framework (AI RMF 1.0)*. <https://www.nist.gov/itl/ai-risk-management-framework>
3. Christiano, P. et al. *Deep Reinforcement Learning from Human Preferences*. arXiv:1706.03741, 2017. <https://arxiv.org/abs/1706.03741>
4. Bezos, J. *Letter to Shareholders* — Type 1/Type 2 decisions (one-way vs. two-way doors). Amazon. <https://www.aboutamazon.com/news/company-news/2016-letter-to-shareholders>

## Adoption notes

- **Declare reversibility on your action/tool catalogue first** — it's the axis everything else keys off; default unknowns to irreversible.
- **Make the approval payload the concrete diff**, never a model summary — that single rule prevents most rubber-stamping.
- **Wire fire-and-judge into the eval loop** — a correction that doesn't become an eval case is a lesson thrown away.
- **Tune fatigue from the bottom up** — raise thresholds and sample on the trivia; never touch the safety floor.

## Change log

| Version | Date | Status | Change |
| --- | --- | --- | --- |
| 0.2.1 | 2026-07-26 | Draft | Recorded transitive generative credit: the SpecOrigin field ADRs this revision draws on were drafted with Claude Opus 5 (Anthropic; 1M context). Frontmatter-only; no semantic change. |
| 0.2.0 | 2026-07-26 | Draft | Added §When judgment happens: **scheduled checkpoints** for long-running runs (declared at plan time; deferrable questions batch to the next checkpoint; blocking ones follow APR-017 escalation; staged phase-boundary approval), and **AI-substituted judging** under two hard conditions (isolated non-producer dispatch; recorded substitution that never satisfies the safety floor). Added the **anchoring** fatigue lever (minimal default selection, dependency-aware deselection). Surfaced by adopter field experience (SpecOrigin draft ADRs 015/016, 2026-07-26). Added APR-017 to `related`. |
| 0.1.0 | 2026-05-31 | Draft | Initial draft published as APR-009. |
