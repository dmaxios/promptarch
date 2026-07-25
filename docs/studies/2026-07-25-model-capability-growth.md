# Study: Does model-capability growth reduce the need for promptware architectural principles?

*Status: **Actioned** (informative). Not itself an APR — a `docs/studies/` entry following the pattern of [`harness-coverage.md`](harness-coverage.md): a question posed, checked against the existing corpus, seeding a change rather than a standalone APR. Its one recommendation is adopted as the bidirectional-re-validation clause in [APR-008](../../principles/APR-008-artifact-lifecycle.md) v0.2.0 (§Model migration) — folded in directly while APR-008 is still Draft — and recorded as absorbed in [`apr-backlog.md`](../../meta/apr-backlog.md) §Considered.*

## Question

As frontier models get more capable, does the need for promptware architectural discipline — the kind PROMPTARCH publishes — shrink with it? If an APR encodes a workaround for a model that can't yet be trusted, a sufficiently capable model should retire that workaround. Put sharply: is PROMPTARCH's corpus a *temporary scaffold* for today's models, or a *durable* discipline?

This isn't a hypothetical objection. It's an active, unresolved debate in the field right now, and it deserves an explicit answer inside the corpus rather than an implicit one.

## Two things "framework layer" bundles

Public discussion of this question conflates two different kinds of structure, which is why it doesn't have a single answer.

**Compensatory structure** exists because a model can't yet be fully trusted: forced multi-step gating, mandatory verification loops, role-play personas that catch a single pass's errors, verbose upfront specification because the model can't reliably infer intent from a short prompt. [The Bitter Lesson of Agent Frameworks](https://browser-use.com/posts/bitter-lesson-agent-frameworks) targets exactly this layer, arguing "99% of the work is done within the model itself" and that predetermined planning modules and restricted action spaces freeze in assumptions a capable model outgrows. This layer *should* shrink as models improve — it is a tax paid to offset a capability gap.

**Coordinating structure** exists for reasons orthogonal to model competence: traceability from requirement to artifact, review gates that let multiple humans align before implementation, audit history, governance calibrated to the consequence of an action rather than to how good the actor is. [Hugo Bowne-Anderson's framing](https://hugobowne.substack.com/p/are-better-models-making-agent-engineering) is apt here: better models don't eliminate agent engineering, they *relocate* it — planning that once needed application logic can happen inside the model, but the questions "what must the agent remember, and what happens when it's wrong" don't go away just because the model got smarter.

The public "do we need frameworks" debate is really an argument about how much of a given framework is compensatory versus coordinating, dressed up as a binary question. A concrete data point: current AI-coding frameworks sit at genuinely different points on this axis. Superpowers and BMAD lean heavily compensatory — mandatory TDD, role-played teams, built around the premise that a single unsupervised model pass can't be trusted. SpecKit and OpenSpec lean toward coordinating — specification-first gating that documents intent for humans as much as it constrains the model. None of the public commentary distinguishes the two axes explicitly; PROMPTARCH's own scope split (below) already does.

## PROMPTARCH already draws this line — check it against the question

[`harness-coverage.md`](harness-coverage.md) splits harness concerns into a **specification/governance face** ("strong and largely complete" coverage) and a **runtime-mechanism face** ("deliberately absent... generic systems engineering, not a promptware-specific principle"). That split maps cleanly onto compensatory vs. coordinating: runtime-mechanism concerns (schedulers, dispatch loops, retry/backoff) are exactly the layer that shrinks as models improve, and PROMPTARCH already scopes them out (⛔) by design, independent of this question. The corpus's actual subject matter — specification and governance — is the coordinating layer. So the coverage matrix already implies an answer; this study makes it explicit and checks it isn't contradicted anywhere in the corpus.

**[APR-000](../../principles/APR-000-promptware.md) makes the strongest version of this argument, structurally, without saying so.** The promptware/codeware boundary is drawn on *interpreter semantics* — "formal language... fixed, mechanical semantics" (codeware) versus content "interpreted at execution time by LLM agents, which assign meaning probabilistically" (promptware) — not on model quality. A more capable model is still a probabilistic interpreter; it does not cross into fixed, mechanical semantics no matter how good it gets. Model capability is a continuous variable *inside* the probabilistic bucket, not a path out of it. The reason promptware needs its own discipline is therefore capability-invariant by construction — it does not erode as models improve, because the property that triggers the discipline never goes away.

## Where capability growth genuinely does bite — and a gap in APR-008

It isn't all "nothing changes." [APR-008](../../principles/APR-008-artifact-lifecycle.md) already treats the model as a declared, eval-validated dependency and requires re-validation on model change — but only in the **regression direction**: a model swap might make an artifact *worse*, and the migration gate blocks that. APR-008 has no symmetric handling for the **over-caution direction**: a gate, threshold, or mandatory-approval step calibrated to a weaker model can become stale dead weight once a stronger model clears it with margin — compensatory structure that nobody is checking has become unnecessary. This is a real, narrow gap, not a wholesale objection to the corpus.

Where the corpus's existing placement logic is *already* capability-independent, this study finds no contradiction: [APR-009](../../principles/APR-009-human-in-the-loop.md) places human oversight by an action's **reversibility and blast radius**, not by model competence — a more capable model doesn't shrink the blast radius of an irreversible action, so this correctly does not soften with capability. [APR-010](../../principles/APR-010-governance.md) governance/conformance and [APR-005](../../principles/APR-005-trust-boundaries.md) trust boundaries are audit- and security-motivated, not competence-motivated, for the same reason.

## Coverage check

| Concern | Erodes with model capability? | Existing corpus position |
| --- | --- | --- |
| Compensatory step-gating / forced planning loops | Yes — should shrink | Runtime-mechanism, out of scope by design (harness-coverage.md) |
| Eval thresholds / regression gates | Partially — recalibrates, doesn't vanish | [APR-008](../../principles/APR-008-artifact-lifecycle.md), regression direction only — **gap**: no stale-over-caution direction |
| Human-approval gate placement | No — calibrated to blast radius, not competence | [APR-009](../../principles/APR-009-human-in-the-loop.md) |
| Governance / conformance machinery | No — audit-motivated | [APR-010](../../principles/APR-010-governance.md) |
| Trust boundaries / untrusted-input handling | No — untrusted input is untrusted regardless of model skill | [APR-005](../../principles/APR-005-trust-boundaries.md) |
| The promptware/codeware boundary itself | No — capability-invariant by construction | [APR-000](../../principles/APR-000-promptware.md) |

## Recommendation

The corpus's foundational scoping already answers the big version of this question correctly — nothing here argues the corpus is a temporary scaffold. The one actionable finding is narrow: **APR-008's model-migration principle is asymmetric.** It gates against regression but has no mechanism for flagging over-cautious debt — gates and thresholds that were right for a prior model and are now excess weight. There is even an in-corpus precedent for the idea: [APR-009](../../principles/APR-009-human-in-the-loop.md) §Avoiding approval fatigue already lets fire-and-judge sampling rates *fall with track record* — performance-relative calibration of rigor is accepted, just not generalized to eval thresholds and gate placement. A candidate backlog idea, in the format `apr-backlog.md` uses:

> **Capability-relative gate calibration** — 💡 Idea (low priority; APR-008 itself is still Draft, so this is an extension, not a coverage gap). One-liner: eval thresholds and gate placements (`min_eval_score`, staged human-approval tiers under APR-009) SHOULD be periodically re-benchmarked against current model capability, so gates calibrated to a weaker model don't silently persist as dead weight. Complements APR-008's regression-direction re-validation with an explicit over-caution-direction check. Relationship: extends APR-008; touches APR-009 only insofar as approval-tier placement may reference eval performance, not blast radius (APR-009's blast-radius criterion itself should NOT change).

This study's finding is essentially reassuring news for PROMPTARCH's premise, with one concrete, small follow-up rather than a challenge to the corpus's reason for existing.

## References

1. Zunic, N. *The Bitter Lesson of Agent Frameworks*. browser-use, 2026. <https://browser-use.com/posts/bitter-lesson-agent-frameworks>
2. Bowne-Anderson, H. *Are better models making agent engineering obsolete?* 2026. <https://hugobowne.substack.com/p/are-better-models-making-agent-engineering>
3. *The Great Framework Showdown: Superpowers vs. BMAD vs. SpecKit vs. OpenSpec vs. GSD*. 2026. <https://rexai.top/en/posts/ai-coding-frameworks-comparison-2026/>

---
*Drafted with Claude Sonnet 5 (Anthropic) at the request of a PROMPTARCH-adjacent reader; offered for consideration, not submitted as an issue or PR by the model itself. If adopted, record per CONTRIBUTING.md: `generative-contributors: ["Claude Sonnet 5 (Anthropic)"]`.*
