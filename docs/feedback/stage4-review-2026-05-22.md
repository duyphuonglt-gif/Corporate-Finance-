# Stage 4 review — 2026-05-22

Reviewing `docs/specs/2026-05-16-luong-iqiyi-spec.md` and `deliverables/prompt-log.md`.

Phuong — this is the most methodologically rigorous Stage 4 spec in the cohort, and not by a small margin. Five rounds of HIL iteration with two independent reconstruction tests, a cross-vendor test against Gemini that surfaced two gaps (§9/§7 divergence + an output-language gap a Claude-only loop would never have caught), the FX-conversion fix that promotes `market_capitalization` to `market_capitalization_rmb` before any Performance ratio runs, the architectural decision in Round 3 to *remove* FY2023 entirely rather than warn against it, and a §7 validation table that explains a 3-basis-point Du Pont ROE mismatch as a year-end methodology artifact rather than treating it as a model error — every one of these moves is past the rubric and into engineering territory. The notes below are not "you need to fix this" — they're four directions you could take *next*, picked because they extend something you've already done well rather than papering over something missing. Take any one of them seriously and you're doing real AI-quant work, not student work.

---

## What the auto-scan confirms

| Signal | Value | Read |
|---|---|---|
| Section coverage | 11/11 | Complete |
| Spec length | ~3,383 words | Above the 1,500–2,500 target band, but the over-length is traceable to 10-gap HIL closure (FX rules, FY2024 absolute-figure prohibitions, English-only language rule) — every paragraph above the band is a closed gap |
| Named-range hits | 384 | Spec speaks the model's language at every level, including a model-specific named range (`CASH_depreciation_amortization`) explicitly declared because Stage 1 didn't ship it |
| Ratio categories in §6 | 6/6 (Performance, Profitability, Efficiency, Leverage, Liquidity, Du Pont) | Every category from the master template covered |
| Ratio table rows in §6 | 30 | Above the 25-ratio rubric expectation |
| Validation rules in §7 | 6 | The Du Pont ROE rule is unusually thoughtful — it explains *why* the direct and decomposed ROE disagree by 3 bps (FY2025 vs. FY2024 year-end equity denominators) rather than asserting they should match |
| HIL iteration rounds | 5 | Round 1 draft → Test 1 (independent Claude reconstruction) → Round 3 (architectural FY2023 removal) → Test 2 → Test 3 (formula-by-formula audit) → Test 5 (Gemini cross-vendor) → v2.0 |
| Cross-vendor coverage | Claude + Gemini | The only spec in the cohort that has been tested against a non-Claude executor; this is what surfaced GAP F (Gemini output in Vietnamese) and GAP 2 recurrence (§9 vs. §7 divergence under a different LLM's resolution heuristic) |
| Prompt log | 10 entries, before/after notes per gap | Each row pairs a meaningful prompt with the spec language that changed because of it — this is closer to a methodology paper than a log |

There is no Stage 4 rubric item where this spec falls short. The rest of this file is forward-looking.

---

## Four directions you could take next

These are ordered by how much new skill they exercise, not by priority — pick whichever interests you. None of them affects your Stage 4 score; this is intellectual extension work that the cohort's strongest students should be doing if they want their portfolios to actually demonstrate AI-quant capability rather than just "I followed the rubric." A couple of these you've already started — the notes below are about pushing what's there one step further.

### 1. Turn your spec into an eval harness (the "specification as benchmark" move)

You've already done the conceptual leg-work for this. Your HIL Test 3 manually verified all 6 validation rules and 30 ratio formulas against the Stage 3 workbook, and your Stage 5 verification recomputed 6 ratios by hand and caught a `shares_outstanding` discrepancy between the Stage 2 and Stage 3 inputs (964,912K vs. 963,044K). That manual recompute *is* an eval harness — it's just running in your head instead of in Python.

The natural follow-up: if the spec is the prompt, then your six validation rules (and the 30 ratio formulas they govern) are an **evaluation harness**. Most Stage 5 work treats the LLM's output as a single deliverable to read. You could treat it as something to **score**, and have a script do what you currently do by hand.

**Concrete next step.** Take your Stage 5 output (`deliverables/2026-05-18-luong-iqiyi-llm-raw.md`) and write a ~40-line Python script that:
- Reads the raw LLM analysis Markdown file.
- Extracts each ratio value the LLM reported (regex against the Appendix table is enough — your §11 already mandates an "Appendix — Ratio Summary Table" in a consistent format, which is exactly the affordance a parser needs).
- Recomputes the expected value from your Stage 3 workbook using the named ranges in §5.
- Reports a pass/fail per validation rule (BS balance, Du Pont ROA reconciliation, Cash Coverage uses `CASH_depreciation_amortization`, no formula errors, units consistent with RMB '000).
- Bonus: rerun the same script against the Gemini output from Test 5 — you'd get a clean tabular diff of which ratios drifted across vendors.

You'd be the first student in the cohort to ship a spec with a programmatic conformance check attached. The artifact takes a few hours and demonstrates something most MBA finance courses don't even ask for: that you can specify a thing precisely enough to grade an AI's execution of it.

### 2. Write up the cross-vendor experiment as a standalone piece

You already did Direction 2 from the Stage 5 default playbook — Test 5 ran the spec through Gemini after a Claude-only loop had declared it done, and the experiment surfaced two genuine gaps (§9 vs. §7 divergence, and Gemini writing the executive summary in Vietnamese). That's a more interesting finding than most students will produce in their entire Stage 5. It's just buried in row 6 of your prompt log.

The portfolio version of this isn't more vendors — it's writing the experiment up as a methodology piece in its own right, because the *finding* is the contribution, not the execution.

**Concrete next step.** Take the Test 5 row of your prompt log and expand it into a 1–2 page write-up: `analysis/explorations/2026-05-22-luong-cross-vendor-spec-execution.md` (or wherever). Cover:

- **The setup.** Same spec v2.0, same wrapper prompt, two LLMs (Claude Sonnet 4.6, Gemini Pro), zero prior context on each.
- **The convergence.** Where did the two models produce identical outputs? (Almost certainly the ratio values themselves — your formula notation is unambiguous enough that arithmetic disagreement is impossible.)
- **The divergence.** Two non-arithmetic gaps surfaced: (a) §9 vs. §7 contradiction resolved differently by Claude (defers to the more detailed §7) and Gemini (defers to the later-positioned §9), and (b) output-language inference — Gemini inferred Vietnamese from author name and file history, Claude defaulted to English. Both are gaps in the spec that single-vendor testing would never have caught.
- **The bonus extension.** If you're feeling completionist: add a third vendor (GPT-4 or GPT-5) to see whether the divergences cluster pairwise (Claude+ChatGPT vs. Gemini, or the other way) and write a sentence about what that suggests about LLM "personalities" in spec-execution.
- **One-paragraph thesis.** What does the divergence pattern tell you about which parts of finance specification are now commodity-AI tasks (arithmetic, formula application) vs. which still require human-authored disambiguation (locale defaults, contradiction resolution between spec sections)?

This is the genre of work being published in industry blogs (Anthropic, OpenAI, McKinsey AI practice) right now. A solid 1–2 page version of it from a finance MBA student is a portfolio piece, not coursework. You've already done the experiment. The remaining work is writing the conclusion you've earned.

### 3. Sensitivity & Monte Carlo on the assumptions

Your spec uses two hardcoded assumptions: `cost_capital = 9.00%` (analyst assumption, including a stated 3% China risk premium) and `tax_rate = 25.00%` (PRC statutory). Both are point estimates. EVA depends on `cost_capital` directly; `currentYear_after_tax_operating_income` depends on `tax_rate` through the (1 − tax_rate) × interest expense term, which then propagates into ROA, ROC, the Du Pont decomposition's Operating Profit Margin, and Debt Burden.

A point-estimate spec produces a point-estimate analysis. A spec that **builds uncertainty in** produces an analysis that distinguishes "iQIYI's EVA is –RMB 1,570M" from "iQIYI's EVA is –RMB 1.57B with an 80% confidence band of –RMB 0.9B to –RMB 2.3B, with the bulk of the uncertainty coming from `cost_capital` (driven by the China risk premium component), not from `tax_rate` or operating performance."

Your Recommendation 5 already names two specific triggers for WACC reassessment (PRC regulatory action, HNTE license renewal risk). That recommendation reads much sharper if it's anchored in a sensitivity result rather than a prose argument.

**Concrete next step.** Add a §12 ("Sensitivity Specification") to your spec that defines:

```markdown
### 12. Sensitivity Specification

The Stage 5 analysis must include a one-page sensitivity appendix
covering the two assumption inputs:

| Input | Point estimate | Range to test | Distribution shape |
|---|---|---|---|
| cost_capital | 9.00% | 6.5% – 13.0% | Triangular, mode at 9.0% |
| tax_rate | 25.00% | 15.0% – 25.0% | Uniform (15% = HNTE rate; 25% = statutory) |

Required outputs:
- One tornado chart showing which input moves EVA the most.
- A 5,000-trial Monte Carlo histogram of EVA with the 10th/50th/90th
  percentile values reported.
- One narrative paragraph: at what value of cost_capital does EVA
  cross zero (i.e., what WACC would iQIYI need to achieve to break
  even on economic profit, holding FY2025 operating results constant)?
- One narrative paragraph: how much of the –RMB 1.57B EVA shortfall
  is "China risk premium" vs. "operating underperformance"?
```

The `tax_rate` range is particularly interesting in iQIYI's case because the lower bound is *not* arbitrary — it's the HNTE preferential rate that your Recommendation 5 already names as expiration risk. So the sensitivity isn't a textbook exercise; it's quantifying a real risk your spec already identifies qualitatively. The Stage 5 LLM can produce this if your spec asks for it. The deliverable becomes a meaningfully better-than-textbook analysis — one that names what you don't know as well as what you do.

### 4. Spec-driven generalization — make the spec Bilibili-runnable (or Tencent Video, or Netflix)

Your spec is iQIYI-specific in roughly five places: §3 (data values), Note A (PP&E gross/net reporting convention — iQIYI reports net), Note B (D&A embedded in COGS — content-platform convention), the FX block in §3 and §5 (USD ADS price → RMB equity book value), and the regulatory/VIE/USD-debt context in Recommendation 5 (PAG Notes, 2028 Notes, HNTE license, PRC content-ban risk). The structure of §1, §2, §4, §6 (formulas), §7 (validation), §8 (analysis requirements), §9 (Du Pont), §10 (recommendation framework), and §11 (output format) is **company-agnostic**.

The deeper observation: a well-written spec should be a parameterized template, and the company-specific values should be an appendix you swap out. Bilibili (BILI, NASDAQ — also ADR, also VIE, also content-cost-heavy) and Tencent Video (privately held within Tencent, but the segment numbers are reported) are the natural test cases — the FX block, the D&A-in-COGS convention, and the VIE/regulatory section in Recommendation 5 would all carry over essentially unchanged. Netflix (NFLX) is a useful stress test in the other direction: same content-platform economics, but no VIE, no FX conversion, no HNTE, no PRC content-ban risk — which surfaces exactly which parts of your spec are iQIYI-specific vs. content-streaming-generic.

**Concrete next step.** Refactor the spec into two files:

- `spec-template.md` — the structure, with `{{COMPANY}}`, `{{TICKER}}`, `{{REPORTING_STANDARD}}`, `{{REPORTING_CURRENCY}}`, `{{FX_RATE}}` (or `null` if reporting in USD natively), `{{D_A_LOCATION}}` (one of `embedded_in_cogs`, `separate_IS_line`, `cf_only`), and a `§3 placeholder` table.
- `2026-05-16-luong-iqiyi-inputs.yaml` (or `.md`) — the iQIYI-specific data values in §3 plus the assumption set (`cost_capital`, `tax_rate`, `fx_rate`, `share_price`, `shares_outstanding`).
- An `appendix-regulatory-risk.md` block per company that captures the §10 Recommendation-5-style regulatory narrative (VIE structure, currency mismatch, license renewal, content-ban risk for iQIYI; whatever the equivalent is for Bilibili, Tencent Video, Netflix).

Then write a 1-paragraph note: *"To run this spec for Bilibili (BILI), replace `iqiyi-inputs.yaml` with `bilibili-inputs.yaml`, swap in `bilibili-appendix-regulatory-risk.md`, and re-run Stage 5. The analysis framework, validation rules, Du Pont decomposition, and unit-translation rules are unchanged. To run for Netflix, additionally null out `fx_rate` and remove the VIE block from the regulatory appendix."*

You will have shipped a **reusable analytical artifact** rather than a one-off deliverable. That's the difference between an MBA project and something you'd put on a senior analyst's GitHub. The HIL-iteration discipline you developed in Rounds 1–5 is exactly what makes this generalization tractable — a spec that survived two independent-reconstruction tests and a cross-vendor test is robust enough to parameterize without breaking.

---

## A small honest reaction to the prompt log

Your prompt log is doing something most students don't even attempt: each row is a **before/after note** that pairs the prompt with the spec language that *changed* because of it. Row 2 (GAP B — FX conversion 0.085x → 0.597x) and row 6 (GAP F — Gemini output in Vietnamese; §11 English-only rule added) are particularly strong because each one walks the reader from "the spec said X" → "the executor did Y" → "the root cause was Z" → "the spec now says X′". That's not a log — that's a methodology artifact. The single most candid thing in the entire log is the line in row 4: *"the spec caused this because §1 provides FY2024 percentages for reader context but never declares that FY2024 IS absolute figures are not in the model — any executor that sees a percentage will attempt to derive the underlying absolute if the spec does not explicitly prohibit it."* That sentence is what spec-engineering writing actually sounds like.

The single way it could be sharper: write a **one-paragraph synthesis at the top of the log** that names the meta-pattern across the 10 gaps you closed. From the rows alone I can see at least three recurring shapes: (a) "the spec provided a percentage for context and the executor back-calculated an absolute" (GAP 5, GAP D), (b) "two spec sections drifted across rounds and a sufficiently different LLM resolved the contradiction differently than expected" (GAP 2 recurrence in Test 5), (c) "a convention the author held implicitly was never written down" (GAP F language, GAP B FX). A 4–6 line opener that names these patterns would turn the log from a chronology into a **taxonomy of spec-failure modes** — which is a piece of writing that would be useful to any future MBA student trying to author specs for LLM execution.

Something like:

```markdown
**Meta-patterns observed across the 10 gaps closed in Rounds 1–5:**

1. *Context-percentages back-calculated to absolutes.* When the spec
   provided a percentage for reader context (e.g., "FY2024 net margin
   2.7%"), executors derived the underlying absolute figure even when
   that figure was outside the model. Fix: explicit prohibition rules
   in §8 stating which years' absolute figures are in-model vs.
   reference-only.
2. *Inter-section drift across rounds.* Sections edited in different
   rounds (§7 in Round 2, §9 left unchanged from Round 1) developed
   contradictory language. Single-vendor testing masked this because
   Claude weighted §7's detail over §9's recency; Gemini did the
   opposite. Fix: single-source-of-truth rule — when a fact lives in
   two sections, cross-reference rather than restate.
3. *Implicit conventions never written.* Output language, currency
   for Performance ratios, and D&A source location were all conventions
   the author held implicitly. Fix: every implicit convention gets an
   explicit declaration in §11 (output format) or §4 (naming).
```

Even this short an opener closes the loop and turns the prompt log itself into a piece of methodology writing rather than just a record of what happened. If you wanted to take it further, that opener is also the spine of an extension piece on "10 ways an LLM can execute a finance spec wrong, and what the spec language did to allow it" — which is a portfolio piece in its own right.

---

## Looking ahead to Stage 5

Stage 5 will grade how cleanly your Stage 1–4 work coheres into a single deliverable. Yours already does — `deliverables/2026-05-18-luong-iqiyi-final-analysis.md` and `2026-05-18-luong-iqiyi-spec-retrospective.md` together already cover everything Stage 5 will ask for, and the manual verification file (5 exact matches + 1 traced rounding discrepancy) is the cleanest reconciliation artifact in the cohort. There is no Stage 5 cleanup work to schedule. Use that bandwidth on one of the four directions above instead. If you do, drop a note in your prompt log or under `analysis/explorations/` so the work is visible; if you don't, your Stage 5 will still come in clean on the rubric and you'll have lost nothing.

The post-deadline revision-sweep window is open if you want to add any of the above to the spec retroactively — bumps to Stage 4 are possible, though they're not the point. The point is whether one of these directions sounds interesting enough to spend a Saturday morning on. Direction 2 in particular is essentially free for you — the experiment is already done, only the write-up is missing — and would convert a buried row in your prompt log into a standalone portfolio artifact. If yes, that's a much better use of the next two weeks than incremental polish on what's already a strong artifact.

---

*This review is feedback-only — no scores included.* Score numbers live in the internal grade report and the instructor's email; this file is intended as colleague-level input on your work, not as a graded artifact.
