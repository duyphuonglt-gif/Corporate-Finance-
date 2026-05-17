---
template: iteration-log
purpose: "Annotated before/after diff documenting spec changes made during HIL review — supports Stage 4 rubric criterion: Spec craft + prompt log quality"
author: "Luong Duy Phuong"
date: "2026-05-16"
course: BUS-629
stage: 4
company: "iQIYI Inc (IQ, NASDAQ)"
spec_file: "docs/specs/2026-05-16-luong-iqiyi-spec.md"
spec_version: "2.0"
rounds: 5
tests: 6
gaps_total: 10
gaps_closed: 10
---

# Stage 4 HIL Iteration Log — iQIYI Inc (IQ)

**Author:** Luong Duy Phuong
**Spec:** `docs/specs/2026-05-16-luong-iqiyi-spec.md` (v2.0)
**Period:** 2026-05-16 — 5 rounds of HIL revision, 6 independent reconstruction tests (Claude Tests 1–4, Gemini Tests 5–6)

Each entry below shows: the spec text **before** the revision, the spec text **after**, and a one-line note explaining what gap the change addressed and why the original caused it.

---

## GAP 2 — Du Pont Time-Mismatch Explanation (§7 + §9) · Round 2 + Round 5

**Why it matters:** GAP 2 is the most dangerous gap in the spec because it is arithmetically invisible. The computed ROE values (–1.56% vs. –1.53%) are both correct — only the *explanation* of why they differ was wrong. Test 1 (Claude) propagated the wrong explanation verbatim. Test 5 (Gemini) re-triggered it because §7 was fixed in Round 2 but §9 was not, creating an internal contradiction that different LLM families resolve differently.

### §7 Validation Table — Du Pont ROE note

| | Text |
|---|---|
| **BEFORE (Round 1)** | `Mismatch source: currentYear_assets_total vs. startYear_equity — methodological artifact, not an error.` |
| **AFTER (Round 2)** | `Mismatch source: RATIO_leverage uses currentYear_equity (FY2025 year-end) as its denominator, while direct ROE uses startYear_equity (FY2024 year-end). Both are internally consistent formulations; the 3-basis-point difference is a methodological artifact, not a computation error. Executor must explain this distinction — do not treat as a model error.` |

**Gap note:** Original conflated two different variables (`currentYear_assets_total` vs. `startYear_equity`). The real mismatch is the same variable (`equity`) at different year-end dates. Wrong because the spec was drafted from the Du Pont formula structure without tracing which year-end each named range actually pulls.

---

### §9 Instruction 2 — Du Pont executor instruction

| | Text |
|---|---|
| **BEFORE (Round 1–4)** | `2. Explain the time-mismatch: Du Pont leverage uses currentYear_assets_total while direct ROA uses startYear_total_assets. This produces Du Pont ROE (–1.56%) ≠ direct ROE (–1.53%). Acknowledge and explain — do not treat as an error.` |
| **AFTER (Round 5)** | `2. Explain the time-mismatch: RATIO_leverage uses currentYear_equity (FY2025 year-end) as its denominator, while direct ROE uses startYear_equity (FY2024 year-end). This produces Du Pont ROE (–1.56%) ≠ direct ROE (–1.53%). Both formulations are internally consistent; the 3-basis-point difference is a methodological artifact, not a computation error. Do not treat as an error.` |

**Gap note:** §9 was never updated when §7 was fixed in Round 2 — creating an internal contradiction. Claude-family tests (Tests 1–4) resolved the contradiction by weighting §7 (more detailed, earlier in spec). Gemini (Test 5) resolved it by deferring to the later §9 instruction, re-triggering the original error. Cross-vendor testing was essential to expose this.

---

## GAP C — Unit Translation Rule (§11) · Round 2

**Why it matters:** Test 1 wrote "RMB 478K" instead of "RMB 478M" — a three-order-of-magnitude error immediately visible to any finance reader. The spec had no rule translating RMB '000 named-range values into prose millions.

### §11 Output Format — unit translation

| | Text |
|---|---|
| **BEFORE (Round 1)** | *(no unit translation rule — §11 silent on RMB '000 vs. millions)* |
| **AFTER (Round 2)** | `Unit translation rule: All named-range values in this spec are in RMB thousands (RMB '000). When presenting values in prose, convert to millions for readability: divide by 1,000 and append "M" (e.g., INC_interest_expense = 909,616 → RMB 909.6M; currentYear_after_tax_operating_income = 478,169 → RMB 478.2M). Do not write "RMB 478K" — that implies RMB 478,000, which is three orders of magnitude too small. Exception: small values below RMB 10M may be written as "RMB X,XXX K".` |

**Gap note:** Named ranges store raw 20-F values in RMB '000 per the Stage 1 template. The spec described formulas and values in that unit but never instructed the executor how to present them in prose. Without an explicit rule, every executor must guess — and guesses diverge.

---

## GAP B — Market-to-Book FX Conversion (§5 + §6) · Round 2

**Why it matters:** `market_capitalization` is in USD '000; `currentYear_equity` is in RMB '000. Dividing two values in different currencies directly — 1,136,292 USD ÷ 13,308,926 RMB = 0.085 — produces a dimensionless number with no financial meaning: the ratio mixes apples and oranges. A valid Market-to-Book ratio requires both inputs in the same currency. Test 1 attempted FX conversion but applied it to the wrong variable; Round 2 identified the correct fix: convert `market_capitalization` to RMB before any computation.

### §3 Analyst Assumptions — fx_rate named range added

| | Text |
|---|---|
| **BEFORE (Round 1)** | FX rate mentioned in §1 narrative only ("RMB 6.9931 = US$1.00") — not available as a named range. |
| **AFTER (Round 2)** | Added to Analyst Assumptions table: `` `fx_rate` \| RMB/USD exchange rate (Federal Reserve H.10, Dec 31, 2025) \| 6.9931 \| RMB per USD `` |

### §5 Derived Inputs — market_capitalization_rmb added

| | Text |
|---|---|
| **BEFORE (Round 1)** | Only `market_capitalization` = 1,136,292 USD '000 defined. No RMB equivalent. |
| **AFTER (Round 2)** | Added: `` `market_capitalization_rmb` \| `market_capitalization × fx_rate` \| 7,946,204 \| RMB '000 `` |

### §6 Performance table — formulas and values updated

| | Text |
|---|---|
| **BEFORE (Round 1)** | MVA = `` `market_capitalization − currentYear_equity` `` = –12,172,634 '000 (mixed USD/RMB). Market-to-Book = `` `market_capitalization / currentYear_equity` `` = 0.085x (mixed USD/RMB). |
| **AFTER (Round 2)** | MVA = `` `market_capitalization_rmb − currentYear_equity` `` = **–5,362,722 RMB '000**. Market-to-Book = `` `market_capitalization_rmb / currentYear_equity` `` = **0.597x**. Unit note rewritten: "All Performance ratio formulas use `market_capitalization_rmb` — never the raw USD figure — so that MVA and Market-to-Book are computed in a single currency (RMB '000)." |

**Gap note:** The spec caused this because `market_capitalization` was defined in §5 as USD '000 (matching the share price input unit) without a conversion step to RMB. Any executor that reads the formula `market_capitalization / currentYear_equity` without a currency-normalization instruction will either compute the mixed-unit ratio silently (producing the meaningless 0.085x) or apply a conversion to the wrong variable. The fix adds `market_capitalization_rmb` as an explicit intermediate named range so no executor can skip the conversion step.

---

## GAP D + GAP 5 — FY2024 Absolute IS Scope Boundary (§8) · Round 4

**Why it matters:** Test 2 cited "a RMB 736.5M profit recorded in FY2024" — a figure absent from the named-range model. The executor back-calculated it from the "+2.7% net margin (FY2024)" in §1 Scope, using FY2025 revenue as a proxy. The spec never declared that IS and CF tabs contain FY2025 data only.

### §8 Analysis Requirements — FY2024 IS scope note

| | Text |
|---|---|
| **BEFORE (Round 3)** | `The executor must address the following for each ratio category, using FY2025 vs. FY2024 year-over-year comparison as the benchmark.` *(no constraint on FY2024 absolute figures)* |
| **AFTER (Round 4)** | Added blockquote immediately after the scope statement: `FY2024 IS scope note: The Income Statement and Cash Flow Statement tabs contain FY2025 data only. FY2024 income statement absolute figures (net income, revenue, COGS, etc.) are not in the named-range model and must not be derived or cited. When referencing FY2024 profitability context, use only the margin percentages stated in §1 (net margin +2.7%, gross margin 24.9%). Do not back-calculate FY2024 absolute net income or revenue from these percentages.` |

**Gap note:** The root cause was that §1 Scope provided FY2024 margin percentages for context — useful for the reader — but never drew a boundary between "figures available in the model" and "figures cited for context only." Any executor that sees a percentage will attempt to derive the underlying absolute figure if the spec does not explicitly prohibit it.

---

## GAP 1 — CASH_depreciation_amortization Named Range (§4) · Round 4

**Why it matters:** Test 1 inferred the correct D&A value (13,264,120) but referenced a named range that does not exist in the Stage 1 template. A Stage 5 executor building from scratch could fail to define the range and get a `#NAME?` error.

### §4 Named Range Conventions — model-specific range declaration

| | Text |
|---|---|
| **BEFORE (Round 3)** | §4 listed standard prefix conventions with no mention of `CASH_depreciation_amortization`. Value was stated numerically in §3 Note B only. |
| **AFTER (Round 4)** | Added blockquote at end of §4: `Model-specific named range — CASH_depreciation_amortization: This named range is not present in the Stage 1 ratios template. It must be defined manually in the model, pointing to the D&A add-back line in the operating section of the Cash Flow Statement tab. Value: 13,264,120 RMB '000. This is the only valid source of D&A for the Cash Coverage ratio — do not substitute INC_depreciation (which equals 0 in this model; see Note B, §3).` |

**Gap note:** Named range conventions are the first thing an executor checks before building formulas. Declaring a custom range in §3 prose (where data inputs live) is the wrong location — an executor follows the convention table in §4 to know what names exist. The fix moved the declaration to where executors look for it.

---

## GAP 3 + GAP A — FY2023 Removal + Gross Margin Formalized (§8 + §6) · Round 3

**Why it matters:** Test 1 cited "27.5% gross margin in FY2023" — a figure sourced from outside the named-range model. FY2023 data exists on the balance sheet tab only; the IS and CF tabs contain FY2025 data only. Any FY2023 reference in the spec invites the executor to hallucinate an external source.

### §8 Scope — FY2023 removal

| | Text |
|---|---|
| **BEFORE (Round 2)** | `FY2023→FY2024→FY2025 where data available` and explicit reference to FY2023 gross margin (27.5%) as baseline. |
| **AFTER (Round 3)** | `FY2025 vs. FY2024 year-over-year comparison only — do not reference FY2023 or any earlier period.` All FY2023 references removed. Profitability baseline reset to: gross margin FY2024: 24.9% → FY2025: 21.1% = 380 bps compression. |

**Gap note:** Removing a year is cleaner than adding a warning. The conditional "where data available" was structurally ambiguous — the executor must guess which tabs have FY2023. Removing the year eliminates the ambiguity at its root.

### §6 Efficiency table — Gross Margin formalized (GAP A)

| | Text |
|---|---|
| **BEFORE (Round 2)** | Gross Margin absent from §6 ratio table. |
| **AFTER (Round 3)** | Added: `Gross Margin \| (INC_sales − INC_cost_goods_sold) / INC_sales \| 21.1% \| %` |

**Gap note:** Test 1 added Gross Margin independently — correct formula, but if a different executor omits it or uses a different label, the appendix ratio summary table will be inconsistent across submissions. Formalizing it in §6 locks the formula and label for all executors.

---

## NEW GAP F — Language Specification (§11) · Round 5

**Why it matters:** Test 5 (Gemini) wrote the executive summary and ratio subsections in Vietnamese, inferring the output language from the author's name and file history. The spec never stated the required output language.

### §11 Output Format — language rule

| | Text |
|---|---|
| **BEFORE (Round 4)** | *(no language specification — §11 stated tone, length, and citation style only)* |
| **AFTER (Round 5)** | Added before Tone instruction: `Language: English only. All prose, headings, labels, and footnotes must be written in English regardless of the executor's default language.` |

**Gap note:** Language is an output format constraint, not a content constraint — the same oversight category as tone and word count. It was omitted because English is the assumed default for an MBA course. Gemini, unlike Claude, did not share that assumption and inferred language from project metadata instead.

---

## Gap Status Summary

| Gap | Section | Round Fixed | Reconstruction Test That Triggered It |
|-----|---------|-------------|---------------------------------------|
| GAP 1 | §4 Named Range Conventions | Round 4 | Test 1 (Claude) — inferred correct value but referenced non-existent named range |
| GAP 2 | §7 Validation + §9 Executor instructions | Round 2 (§7) + Round 5 (§9) | Test 1 (Claude) initial trigger; Test 5 (Gemini) re-trigger via §9/§7 contradiction |
| GAP 3 | §8 Scope — FY2023 removal | Round 3 | Test 1 (Claude) — cited FY2023 gross margin from outside named-range model |
| GAP 4 | §10 Recommendations — VIE/currency topic | Round 2 | Test 1 (Claude) — all 4 recommendations addressed only debt/margin/asset/liquidity |
| GAP 5 | §8 IS/CF scope boundary | Round 4 | Test 2 (Claude) — same root cause as GAP D; fixed simultaneously |
| NEW GAP A | §6 Gross Margin definition | Round 3 | Test 1 (Claude) — added independently with inconsistency risk across executors |
| NEW GAP B | §6 Market-to-Book unit note | Round 2 | Test 1 (Claude) — silently applied FX conversion not in template |
| NEW GAP C | §11 Unit translation rule | Round 2 | Test 1 (Claude) — wrote "RMB 478K" instead of "RMB 478M" |
| NEW GAP D | §8 FY2024 absolute IS prohibition | Round 4 | Test 2 (Claude) — back-calculated FY2024 net income from FY2025 revenue proxy |
| NEW GAP F | §11 Language specification | Round 5 | Test 5 (Gemini) — wrote executive summary in Vietnamese |

---

---

# Part B — Chronological Iteration Narrative

*This section preserves the full first-person reasoning behind each HIL round. It records not just what changed but why each gap existed, why the test revealed it, and what the decision logic was for each fix — the audit trail that a compressed table format cannot carry.*

---

## Round 1 — Spec Draft

I fed four sources into the drafting session before writing a single line of spec: the Stage 4 brief (fetched from the raw GitHub URL), the spec template (also fetched raw), the Stage 1 ratios template (`performance-ratios-template.xlsx`), and the Stage 3 populated workbook (`2026-05-14-luong-iqiyi-financials.xlsx`). Having both the template structure and the live financial values in context meant the LLM could populate formulas and computed figures in a single pass rather than leaving placeholders.

Before drafting, I asked the LLM to surface all assumptions it needed to make — rather than let it proceed silently and embed assumptions I couldn't audit later. Four clarification questions were answered upfront:

1. **Intended audience:** MBA academic — analysis should balance financial interpretation with methodology explanation.
2. **Benchmark:** Standalone trend only — iQIYI FY2024 vs. FY2025, no external peer comparison.
3. **Recommendation focus:** Management perspective, emphasizing operational improvements.
4. **D&A / Cash Coverage treatment:** iQIYI embeds all D&A in COGS, so `INC_depreciation` = 0. I specified that Cash Coverage must use `CASH_depreciation_amortization` from the CF Statement (13,264,120) and that the spec should document this exception explicitly so the Stage 5 executor doesn't silently use zero.

I noticed that specs which skip pre-draft clarification tend to embed the LLM's default assumptions invisibly — particularly around benchmark scope and D&A treatment. By surfacing these upfront and recording the answers, I created an auditable decision trail rather than having to reverse-engineer what the LLM assumed after the fact.

Round 1 output: 11 sections populated, 25+ ratios defined with named-range formulas and computed values drawn directly from the Stage 3 workbook.

---

## Round 2 — HIL Test 1 (Claude, zero prior context)

After Round 1, I ran two parallel review steps rather than just editing the spec myself. First, I asked Claude to re-read the spec independently and identify gaps before I tested it — this surfaced 5 gaps through self-review. Second, I fed the spec to a completely separate LLM session with zero prior context and asked it to (a) reconstruct the ratio model from the named ranges alone and (b) produce a full analysis.

I chose this two-pass approach because self-review by the same LLM that wrote the spec tends to miss gaps it already "knows" from context. The independent reconstruction test forces the spec to stand on its own — if an executor with no prior knowledge produces wrong output, the spec is the problem, not the executor.

### Gaps identified in Test 1

| Gap | Description | Manifested in test? |
|-----|-------------|---------------------|
| GAP 1 | `CASH_depreciation_amortization` not a defined named range in Stage 1 template | Partial — LLM inferred correct value but referenced a non-existent named range |
| GAP 2 | Du Pont mismatch explanation incorrectly attributed to "assets" not "equity" | ✅ Yes — LLM propagated the wrong explanation verbatim |
| GAP 3 | FY2023 gross margin (27.5%) cited in §8 with no source in spec | ✅ Yes — LLM used figure without any citation; hallucination risk confirmed |
| GAP 4 | No recommendation topic for VIE/currency/regulatory risk | ✅ Yes — all 4 recommendations addressed debt/margin/asset/liquidity only |
| GAP 5 | §2 did not state IS and CF are current-year only | Not triggered in this test |
| NEW GAP A | Gross Margin not formally defined in §6 ratio table | ✅ LLM added it independently — inconsistency risk if different LLM omits it |
| NEW GAP B | Market-to-Book formula unspecified on FX conversion | ✅ LLM silently applied FX conversion not in spec, creating formula inconsistency |
| NEW GAP C | No unit translation instruction — RMB '000 values expressed incorrectly in prose | ✅ LLM wrote "RMB 478K" instead of "RMB 478M" (3-order-of-magnitude error) |

### Most consequential gaps

I found GAP 2 and GAP C to be the most consequential, for opposite reasons. GAP 2 was invisible arithmetically — the reconstruction test produced numbers that looked correct, but the explanation of why Du Pont ROE differs from direct ROE was wrong. The spec said the mismatch came from `currentYear_assets_total` vs. `startYear_equity`, which conflates two different variables entirely. The real cause is that `RATIO_leverage` uses `currentYear_equity` while direct ROE uses `startYear_equity` — the same variable, but different year-end dates. Without the reconstruction test I would never have caught this because the numbers passed. GAP C was the opposite: immediately obvious to any finance reader — "RMB 478K" instead of "RMB 478M" is a three-order-of-magnitude error that no unit translation rule in the spec allowed the executor to avoid.

### Round 2 fixes applied

- **GAP 2 (§7):** Rewrote the Du Pont mismatch explanation to correctly identify `currentYear_equity` (used by `RATIO_leverage`) vs. `startYear_equity` (used by direct ROE) as the source of the discrepancy.
- **GAP 4 (§10):** Added a 5th required recommendation topic covering VIE/currency/regulatory risk, specifying the USD debt vs. RMB cash flow mismatch and HNTE license renewal risk as required discussion points.
- **GAP B (§3 + §5 + §6):** The spec listed `market_capitalization / currentYear_equity` without flagging that the two inputs are in different currencies (USD '000 vs. RMB '000). Dividing them directly produces a dimensionless ratio with no financial meaning — 1,136,292 ÷ 13,308,926 = 0.085 is not a valid Market-to-Book. I added `fx_rate` = 6.9931 as a named range in §3 Analyst Assumptions, then added `market_capitalization_rmb = market_capitalization × fx_rate` = 7,946,204 RMB '000 as a derived input in §5, and updated §6 formulas to use `market_capitalization_rmb` throughout: Market-to-Book = **0.597x**, MVA = **–5,362,722 RMB '000**. The unit note was rewritten to require FX conversion before any Performance ratio computation.
- **GAP C (§11):** Added an explicit unit translation rule stating that all named-range values are in RMB '000 and prose must express them in RMB millions (divide by 1,000), with a worked example.

### Gaps deferred after Round 2

- **GAP 1:** The value for `CASH_depreciation_amortization` (13,264,120) is stated numerically in §3, so Stage 5 risk is low even without a formal named range. Deferred.
- **GAP 3:** Added an explicit ⚠️ warning that FY2023 gross margin (27.5%) is sourced from Form 20-F FY2023 — not derivable from the Stage 3 workbook — and that the executor must cite the external source. (Later eliminated entirely in Round 3.)
- **GAP 5:** The IS/CF current-year scope constraint was not triggered in the reconstruction test. Deferred.
- **NEW GAP A:** The independent LLM correctly added Gross Margin on its own; inconsistency risk noted but deferred.

---

## Round 3 — Architectural Decision: FY2023 Removal

I noticed that §8 still referenced FY2023 gross margin (27.5%) even after Round 2 — the Stage 5 LLM executor would encounter a year that exists only partially in the workbook (balance sheet only; not carried into the income statement or cash flow tabs), forcing it to source the figure from outside the spec. That is exactly the condition that produced GAP 3 in the first place. Leaving any FY2023 reference in the spec is a structural invitation to hallucinate.

I proposed removing FY2023 entirely so the executor only ever touches data it can actually derive from the named ranges — a two-year comparison (FY2025 vs. FY2024) is fully sufficient for the Stage 4 rubric and eliminates the hallucination risk at its root. Removing a year is structurally cleaner than adding a warning: the conditional "where data available" forces the executor to guess which tabs have FY2023 data. Removing the year eliminates the ambiguity entirely.

### Round 3 fixes applied

- **§8 scope instruction:** Changed from "FY2023→FY2024→FY2025 where data available" to "FY2025 vs. FY2024 year-over-year comparison only; do not reference FY2023." All FY2023 profitability commentary updated to two-year framing: gross margin FY2024 24.9% → FY2025 21.1% = 380 bps compression.
- **§6 Gross Margin (GAP A closed):** With FY2023 removed, I also closed the deferred GAP A — Gross Margin was still not formally defined in §6, meaning any executor that added it independently might use a different formula or label. Added explicitly as `(INC_sales − INC_cost_goods_sold) / INC_sales` = 21.1% (FY2025), preventing inconsistency across different LLM executors.

Gap status after Round 3: 6 of 8 identified gaps closed.

---

## Round 4 — HIL Test 2 (Claude, zero prior context) + NEW GAP D Discovery

After Round 3 fixes (FY2023 removal, GAP A closure), I ran a second independent reconstruction test — feeding the updated spec to a fresh LLM session with no prior context — to verify that the Round 2 and Round 3 changes held. All 8 previously identified gaps confirmed closed: `CASH_depreciation_amortization` used correctly, Du Pont mismatch explanation correct, no FY2023 references, Recommendation 5 covered VIE/currency/regulatory risk, all figures in RMB millions, and Gross Margin formally included. The spec held.

### NEW GAP D — FY2024 absolute IS figures derived outside named-range model

I noticed that the Test 2 executive summary cited "a RMB 736.5M profit recorded in FY2024" — a figure that does not exist anywhere in the named-range model. Tracing backward, I found that the executor had back-calculated it from the "+2.7% net margin (FY2024)" line in §1 Scope, using FY2025 revenue (RMB 27,291.3M) as a proxy for FY2024 revenue: 27,291.3M × 2.7% ≈ 736.9M. The spec never said FY2024 revenue was the same as FY2025 — the executor filled the gap silently. This is exactly the pattern that produced GAP 3: a percentage in the spec without an explicit sourcing boundary invites the executor to derive an absolute figure that cannot be verified from named ranges.

The root cause is that the spec provides FY2024 margin percentages in §1 for context but never states that FY2024 income statement absolute figures are absent from the model. The IS and CF tabs contain FY2025 only — this was never declared anywhere in the spec, which is also the deferred GAP 5.

### Round 4 fixes applied

- **GAP D + GAP 5 (§8):** Added a blockquote note at the top of §8 Analysis Requirements stating that IS and CF tabs contain FY2025 data only, that FY2024 absolute figures must not be derived or cited, and that executors should reference FY2024 profitability using only the margin percentages stated in §1. This fix simultaneously closes GAP D and the long-deferred GAP 5 in a single targeted addition — both gaps shared the same root cause: the spec never declared the IS/CF scope boundary.
- **GAP 1 (§4):** I decided to close it now rather than carry it to Stage 5. The risk of a Stage 5 executor referencing a non-existent named range — even if inferring the correct value — is worth eliminating. The right place to declare this is §4 Named Range Conventions, not §3, because that is where an executor would look to understand which names are available. Added a blockquote note explicitly flagging `CASH_depreciation_amortization` as a model-specific range absent from the Stage 1 template, specifying the value (13,264,120 RMB '000), and cross-referencing Note B in §3 to prevent any substitution of `INC_depreciation`.

Gap status after Round 4: all 9 identified gaps closed.

---

## Round 5 — HIL Test 3 (Claude) + Full Formula Audit

I ran a third independent reconstruction test against the Round 4 spec. The executor performed explicit formula-by-formula reconstruction — a more rigorous check than Tests 1 and 2 — computing every ratio from named ranges and comparing against spec values. All 9 gaps passed: `CASH_depreciation_amortization` used correctly, Du Pont mismatch explained correctly, no FY2023 or FY2024 absolute IS figures appeared, VIE/currency/regulatory covered in Recommendation 5, all prose figures in RMB millions. The executor also correctly identified Market-to-Book = 0.085x per the `market_capitalization / currentYear_equity` formula — consistent with the Stage 1 template convention.

I used Test 3's line-by-line reconstruction as the basis for a complete audit of all 30 ratio formulas and computed values in §5 and §6 against the Stage 3 workbook. Every formula and value verified correct. Spec promoted to v2.0.

---

## Round 5 (continued) — HIL Test 5 (Gemini, zero prior context) + Cross-Vendor Findings

I ran a fifth independent reconstruction test using Gemini — a different LLM family than the Claude-based tests used in Tests 1–4 — to check whether the spec was executor-agnostic, not just Claude-compatible. Tests 1–4 were all within the same model family, so Gemini provided a cross-vendor check on whether the spec instructions were unambiguous enough to survive a model with different priors and formatting defaults.

### GAP 2 re-triggered — §9 vs. §7 internal contradiction

I noticed that Gemini reproduced the original GAP 2 error: it described the Du Pont mismatch using assets terminology — "leverage uses `currentYear_assets_total` while direct ROA uses `startYear_total_assets`" — which is the same wrong explanation that §7 had correctly fixed in Round 2. Tracing the cause, I found that §9 instruction 2 still contained the original wrong language from before Round 2. §7 and §9 had diverged: §7 was correctly updated in Round 2 to cite `currentYear_equity` vs. `startYear_equity`, but §9 was never updated to match. Gemini read §9 last and overwrote the correct §7 framing with the contradicting §9 instruction.

I found the §9/§7 contradiction the most instructive finding across all five tests. Tests 1–4 all used Claude, which likely resolved the contradiction by weighting §7 (more detailed, earlier in the spec) over §9 (shorter, later). Gemini resolved it differently — by following the most recent explicit instruction — and therefore reproduced the original GAP 2 error. This revealed that the spec had a latent internal inconsistency that the Claude-family tests masked. Cross-vendor testing is essential for exactly this reason: different models have different heuristics for resolving contradictions, and a spec that only passes with one model family is not robust.

### NEW GAP F — §11 missing language specification

I noticed Gemini wrote the executive summary and several ratio subsections in Vietnamese. The spec had never stated what language the output should be in, so Gemini defaulted to a language it inferred from the project context (Vietnamese-named author, Vietnamese file history).

### Round 5 fixes applied

- **§9 instruction 2:** Rewrote the time-mismatch description to match §7 exactly: "`RATIO_leverage` uses `currentYear_equity` (FY2025 year-end) as its denominator, while direct ROE uses `startYear_equity` (FY2024 year-end). This produces Du Pont ROE (–1.56%) ≠ direct ROE (–1.53%). Both formulations are internally consistent; the 3-basis-point difference is a methodological artifact, not a computation error. Do not treat as an error."
- **§11 language specification (NEW GAP F):** Added "Language: English only. All prose, headings, labels, and footnotes must be written in English regardless of the executor's default language."

Gap status after Round 5: 10 of 10 gaps closed. Cross-vendor test (Gemini) confirmed spec is executor-agnostic.
