# Stage 5 review — 2026-05-22

Reviewing `deliverables/2026-05-18-luong-iqiyi-final-analysis.md`, `deliverables/2026-05-18-luong-iqiyi-spec-retrospective.md`, the verification table, and the iteration log at `analysis/validation/2026-05-16-luong-iqiyi-stage4-iteration.md`.

Phuong — this is the most methodologically sophisticated Stage 5 submission in the cohort. The cross-vendor testing (Claude + Gemini), the 5-round HIL iteration with 10 documented gaps closed, the FX conversion catch that produced a 7-order-of-magnitude error in spec v1 (Market-to-Book = 0.085x dropping to the correct 0.598x in v2.0), and the §7/§9 internal-contradiction discovery where Claude deferred to §7 and Gemini deferred to §9 — none of this was asked for by the rubric. You did it because the work *required* it, and you documented it well enough that it stands on its own as a methodology study. That is the disposition the course is trying to teach, and you are already past where most of the assignment is aiming.

The notes below are not a fix-list. They're four directions for what to do with what you've built, plus one small editorial observation on the final analysis itself.

---

## What the auto-scan picks up

| Artifact | Status |
|---|---|
| Raw LLM output | ✓ `deliverables/2026-05-18-luong-iqiyi-llm-raw.md` |
| Manual verification table | ✓ 24 data rows, Match? column present |
| Final analysis | ✓ 6,561 words, 131 ratio citations, 6/6 required sections |
| Spec retrospective | ✓ 2,265 words, 5/5 template signals |
| Prompt log | ✓ `deliverables/prompt-log.md` |
| Stage 2 feedback response | ✓ `docs/decisions/2026-05-18-luong-stage4-feedback-response.md` |
| Repo polish | LICENSE ✓, .gitignore ✓, READMEs 12/12, filename canon 10/10 |

Every rubric artifact present and substantive. The auto-scan didn't surface any packaging gaps — the only signal slightly off-spec is final-analysis length (6,561 words vs. the 1,200–1,800 target), which I'll discuss at the bottom as a small editorial point.

---

## Three things worth naming specifically

These are the parts of the submission that I want to call out *by name* because they're hard and you did them well.

**1. The FX conversion catch.** From your retrospective Gap 1: *"Market-to-Book computed as market_capitalization (USD '000) / currentYear_equity (RMB '000) = 1,136,292 / 13,308,926 = 0.085x. MVA computed in mixed units with no financial meaning. The error is arithmetically invisible: 0.085x is a plausible-looking ratio. Without knowing that market cap should be in RMB, a reader would not flag it."*

That second sentence is the analytical observation behind the catch — that the error survives review because the output looks reasonable. This is the genre of bug that hides in production financial models for years and surfaces only when someone runs an external check. Catching it at Stage 4 by *thinking dimensionally about the formula units before trusting the output* is the kind of discipline that takes most analysts a decade to develop.

**2. The §7/§9 cross-vendor contradiction discovery.** Your retrospective documents that Claude consistently deferred to §7 (validation table) when §7 and §9 contained different language about the Du Pont mismatch, while Gemini deferred to §9. Both were valid resolutions of an ambiguous spec. The fact that *which model you use determines which wrong answer you get* — and that this is invisible if you only test one model — is a finding that should be the headline result of someone's master's thesis on LLM-assisted analysis, not a sidebar in a Stage 4 retrospective.

**3. The accumulated-deficit framing in the final analysis.** From §2.1: *"a structural signal. The market is pricing in the probability that iQIYI's future cash flows will not recover the equity base, a rational judgment given the –RMB 42.7B accumulated retained-earnings deficit that overwhelms RMB 56.0B of paid-in capital to produce positive book equity of only RMB 13.3B."* This is the move from "what the ratio says" to "what the ratio is *signalling about a structural balance-sheet condition*" — the move that distinguishes ratio reporting from ratio analysis. Most submissions stop at "MVA is negative, that's bad." You went one layer deeper and named the mechanism.

---

## Four directions to take what you've built

You've already executed two of the four aspirational directions I'd suggest to most strong Stage 4 students (cross-LLM testing, and the dimensional rigor that comes with spec-as-eval-harness thinking). What follows are four *new* directions that build on what you've already done — not generic next-steps for any strong submission.

### 1. Publish the cross-vendor methodology study externally

What you have at `analysis/validation/2026-05-16-luong-iqiyi-stage4-iteration.md` plus the retrospective is **a methodology study that doesn't exist in the public literature**: a structured comparison of two frontier LLMs on the same financial analysis spec, with documented divergences traced to specific spec ambiguities, with a clear methodology for finding them (cross-vendor testing as a contradiction-detection mechanism). Anthropic's Cookbook, the OpenAI evals repo, and McKinsey's AI practice would all find this useful — and almost no finance MBAs publish in this space.

**Concrete next step.** Take the iteration log + retrospective and rewrite them as a 1,500-word Medium or LinkedIn post:

- **Title direction:** *"Why I tested my financial analysis spec on two LLMs (and what the differences revealed)"*
- **Structure:**
  - Setup (one paragraph): what's a spec-driven financial analysis, what's HIL iteration, why I tested across vendors.
  - The three most interesting gaps (FX conversion, Cash Coverage D&A source, §7/§9 contradiction), each with the *vendor diff* foregrounded.
  - The methodology takeaway: cross-vendor testing as a latent-contradiction-detection method.
  - One-paragraph conclusion: what this means for anyone building LLM-assisted analytical workflows.

Publish under your name with a link back to the GitHub repo. This becomes the most-shareable artifact in your portfolio.

### 2. Generalize the spec into a reusable framework

You've already done the hard work of identifying what makes a spec *robust*: explicit unit conversions, source-annotated model-specific named ranges, single-source-of-truth rules across sections. From your retrospective Part C:

> "Any spec covering a company that reports in one currency but has market data in another (common for ADR-listed companies) should include a standalone pre-computation section before §6 ratio definitions."

This insight applies to *any* multi-currency analysis. Most BUS-629 students will face this in their careers — Vietnamese ADRs, Hong Kong-listed entities with mainland operations, European multinationals reporting in EUR with USD revenue. Your spec, refactored to be company-agnostic, would be a genuinely useful template.

**Concrete next step.** Refactor `docs/specs/2026-05-16-luong-iqiyi-spec.md` into a two-file structure:

- `docs/specs/spec-template-multicurrency.md` — your spec with company-specific values templated (`{{COMPANY}}`, `{{REPORTING_CURRENCY}}`, `{{MARKET_DATA_CURRENCY}}`, `{{FX_RATE}}`, etc.), and your three revision principles (unit conversion block, source-annotated named ranges, single-source-of-truth) made explicit as required template sections.
- `docs/specs/inputs/2026-05-16-luong-iqiyi-inputs.yaml` — the iQIYI-specific values that fill the template.

Then write a one-paragraph "How to use this template" note. The template becomes a contribution that future BUS-629 cohorts can build on (if instructor adopts it) or that you can deploy on your own work.

### 3. Build an HIL methodology playbook from the iteration log

Your iteration log documents 5 rounds × 2 vendors × 10 gaps. That's a structured dataset of *what kinds of gaps emerge at what round of iteration, and which vendor's behavior surfaces them*. From your retrospective's process-feedback section:

> "Adding a 'Discovery round' column would allow analysts and instructors to distinguish early-round structural problems (under-specified draft) from late-emerging edge cases (adequate for primary model, not vendor-agnostic). This distinction is meaningful for rubric assessment."

This is the seed of a **playbook** — a "what to look for at what round" document. Round 1 catches the gross under-specification (units, sources). Round 2–3 catches structural ambiguity within a single vendor. Round 4–5 catches cross-vendor inconsistency. That progression is itself a finding.

**Concrete next step.** Write a 800–1,200-word document at `analysis/methodology/hil-iteration-playbook.md` titled *"A 5-round HIL iteration playbook for spec-driven financial analysis."* Structure:

| Round | What this round catches | What testing method exposes it | Example from iQIYI |
|---|---|---|---|
| 1 | Gross under-specification (units, sources, scope) | Self-review / dry execution | FX conversion missing |
| 2 | Single-vendor structural ambiguity | First LLM execution | §7 wrong-variable reference |
| 3 | Scope creep (FY2023 references with no model support) | First LLM execution | FY2023 removed in v2.0 |
| 4 | Cross-section consistency | Manual re-read of full spec | §7/§9 still inconsistent |
| 5 | Cross-vendor ambiguity | Second LLM (different family) | Gemini surfaces §9 contradiction |

Close with one paragraph: *"What the round progression reveals about spec quality."* This is a methodology artifact — and methodology artifacts are what get cited.

### 4. Apply the framework to a second company in a different domain

The iQIYI spec is one data point. The methodology you developed is a framework. To prove the framework generalizes, apply it to a second company in a meaningfully different domain — somewhere the multi-currency / VIE-structure / ADR-listing constraints don't dominate. Suggestion: a Vietnamese SOE on HoSE, or a state-owned enterprise like Petrovietnam (PVD, PVS) where the analytical interest is government-ownership-as-implicit-credit-guarantee rather than VIE legal-structure risk. Or a real Vietnamese ADR like VinFast, where the constraints overlap with iQIYI (cross-listed, multi-currency, contested governance structure) but the industry context is automotive rather than streaming.

**Concrete next step.** Pick one company. Run your framework through it end-to-end (Stage 1 template → Stage 3 inputs → Stage 4 spec using your template from Direction 2 → Stage 5 analysis with cross-vendor testing). Document the comparison in a `analysis/case-studies/comparative-application.md` file: where did the framework hold up? Where did it need extension? This is portfolio-extension work that takes ~1–2 weekends and produces a second case study that demonstrates *generalizability*.

---

## One small editorial observation

The final analysis is **6,561 words** against a 1,200–1,800-word target. The length is justified by the analytical depth — but the rubric scanner reads length as a polish signal, and "1,500-word executive memo" is a different deliverable than "5,000-word research note." Both have their place; the assignment asked for the former.

This is not a fix to make for Stage 5 — your content earns its length. But for the methodology playbook (Direction 3) and the LinkedIn writeup (Direction 1), tightening matters more. A reader who arrives via a LinkedIn share has 5 minutes, not 30. If you draft either, the discipline of cutting your iQIYI analysis to its 1,500-word skeleton first — keeping the FX-catch story, the §7/§9 contradiction, and one strategic recommendation, cutting everything else — will sharpen the publishable version meaningfully.

---

## Looking forward

The Stage 5 deadline is the formal endpoint of BUS-629, but none of the four directions above need to land before that — they're for after. The post-deadline revision sweep is open if there's any specific Stage-5 edit you want to make (none of the auto-scan signals demand it; everything is in order). The bigger leverage is in what you do *after* the course closes: any one of the four directions above turns this project from an MBA deliverable into a real portfolio artifact, and the foundation is already in place.

The single most valuable thing you could do this week, if you have one Saturday morning free, is **Direction 1** (the public writeup). The work is already done — it just needs to be reframed for an external audience. That's the artifact that gets shared.

---

*This review is feedback-only — no scores included.* Score numbers live in the internal grade report and the instructor's email; this file is intended as colleague-level input on your work, not as a graded artifact.
