---
template: spec-retrospective
purpose: "Structured self-evaluation of the Stage 4 spec — section-by-section verdicts, top three gaps with evidence, revisions, effectiveness rating, and forward link"
audience: student
fields_required: [section_verdicts, top_gaps, revisions, effectiveness_rating, forward_link, process_feedback]
naming_convention: "YYYY-MM-DD-{lastname}-{company-slug}-spec-retrospective.md"
course: BUS-629
stage: 5
company: "iQIYI Inc (IQ, NASDAQ)"
notes: "Retrospective covers spec v2.0 (docs/specs/2026-05-16-luong-iqiyi-spec.md) — 5 rounds of HIL iteration, 6 cross-vendor tests (Claude + Gemini), 10 gaps identified and closed. Evidence sourced from: analysis/validation/2026-05-16-luong-iqiyi-stage4-iteration.md (iteration log), deliverables/2026-05-18-luong-iqiyi-llm-raw.md (raw LLM output), deliverables/2026-05-18-luong-iqiyi-final-analysis.md Section 5 (LLM evaluation)."
---

# Spec Retrospective — iQIYI Inc (IQ) FY2025 Ratio Analysis

**Author:** Luong Duy Phuong
**Spec evaluated:** `docs/specs/2026-05-16-luong-iqiyi-spec.md` (v2.0)
**Raw LLM output evaluated:** `deliverables/2026-05-18-luong-iqiyi-llm-raw.md`
**Course:** BUS-629 · University of Hawaiʻi at Mānoa, Shidler College of Business

---

## Part A — Section-by-Section Verdict

| Spec Section | Verdict | Symptom in LLM output that justifies the verdict |
|---|---|---|
| §1 Scope | **Clear** | LLM correctly bounded analysis to FY2025 standalone vs. FY2024; no scope creep observed |
| §2 Model Architecture | **Clear** | Named-range conventions applied consistently throughout; no `#REF` or undefined range errors |
| §3 Analyst Assumptions | **Vague → Clear** | v1 omitted `fx_rate`; earlier Claude test divided market cap (USD) by equity (RMB) without conversion, producing Market-to-Book = 0.085x. Fixed in v2.0: `fx_rate = 6.9931` added as explicit named assumption |
| §4 Named Range Conventions | **Vague → Clear** | v1 did not declare `CASH_depreciation_amortization` as a model-specific range or note that `INC_depreciation = 0`. Risk: any executor would look for D&A on the IS, find zero, and collapse Cash Coverage to TIE. Fixed in v2.0: `CASH_depreciation_amortization = 13,264,120` declared in §4 with CF Statement source annotation |
| §5 Derived Inputs | **Missing → Clear** | v1 had no `market_capitalization_rmb` derived input. The FX conversion step was invisible — executor applied the formula as written in two different currencies. Fixed in v2.0: `market_capitalization_rmb = market_capitalization × fx_rate` added with computed value 7,946,204 RMB '000 |
| §6 Performance ratios | **Vague → Clear** | v1 formulas referenced `market_capitalization` (USD) directly; executor computed MVA and Market-to-Book across currencies. Fixed in v2.0: all Performance formulas updated to `market_capitalization_rmb`; unit note added requiring FX conversion before any Performance ratio computation |
| §6 Efficiency — Gross Margin | **Missing → Clear** | v1 did not include Gross Margin in the §6 ratio table. LLM cited 21.1% in prose but had no formal named-range formula anchor. Fixed in v2.0: `Gross Margin = (INC_sales − INC_cost_goods_sold) / INC_sales` added to §6 Efficiency table |
| §7 Validation — Du Pont note | **Vague → Clear** | v1 §7 described the Du Pont vs. direct ROE mismatch incorrectly, referencing `currentYear_assets_total` instead of `currentYear_equity`. Earlier Claude test reproduced the wrong explanation verbatim. Fixed in Round 2: §7 rewritten to correctly identify `currentYear_equity` (FY2025) vs. `startYear_equity` (FY2024) as the source of the 3 bp artifact |
| §8 Analysis Requirements | **Vague → Clear** | v1 referenced FY2023 trend analysis without FY2023 named ranges in the model — creating an open hallucination window. Also lacked an explicit boundary on FY2024 IS absolute figures. Fixed in v2.0: FY2023 removed from scope entirely (architectural decision, Round 3); §8 added explicit prohibition on deriving FY2024 absolute IS values |
| §9 Du Pont Executor Instructions | **Vague → Clear** | After Round 2 fixed §7, §9 was not simultaneously updated and retained original wrong language. Gemini Test 5 deferred to §9 over §7 and reproduced the original error — a different resolution heuristic than Claude. Spec had internal contradiction. Fixed in Round 5: §9 fully rewritten to mirror §7 with identical language |
| §10 Recommendations | **Clear** | LLM produced 5 ratio-anchored recommendations covering all major risk areas. No structural gaps in this section |
| §11 Output Format | **Missing → Clear** | v1 had no unit translation rule (earlier Claude test wrote "RMB 478K" instead of "RMB 478M" — 3-order-of-magnitude error) and no language specification (Gemini Test 6 produced one Vietnamese word). Fixed in v2.0: unit translation rule and English-only language rule added to §11 |

---

## Part B — Top Three Gaps with Evidence

### Gap 1 — FX Conversion Missing for Performance Ratios (GAP B)

**Where it surfaced:** Earlier Claude test against spec v1. Market-to-Book computed as `market_capitalization (USD '000) / currentYear_equity (RMB '000)` = 1,136,292 / 13,308,926 = **0.085x**. MVA computed in mixed units with no financial meaning.

**What the spec caused:** §5 Derived Inputs listed `market_capitalization = share_price × shares_outstanding` with value 1,136,292 USD '000, but provided no instruction to convert to RMB before dividing by `currentYear_equity` (RMB '000). The executor applied the formula as written. A dimensionless ratio produced by dividing two values in different currencies is not a financial error the LLM can self-detect — there is no internal signal that the output is wrong.

**This is the most critical gap** because the error is arithmetically invisible: 0.085x is a plausible-looking ratio. Without knowing that market cap should be in RMB, a reader would not flag it. Only cross-referencing the formula units reveals the problem.

**Exact spec language added (v2.0):**
- §3: `| fx_rate | RMB/USD exchange rate (Federal Reserve H.10, Dec 31, 2025) | 6.9931 | RMB per USD |`
- §5: `| market_capitalization_rmb | market_capitalization × fx_rate | 7,946,204 | RMB '000 |`
- §6 unit note: *"market_capitalization (USD '000) must be converted to RMB '000 before use: market_capitalization_rmb = market_capitalization × fx_rate. All Performance ratio formulas use market_capitalization_rmb — never the raw USD figure."*

**Correct values in raw LLM output:** Market-to-Book = 0.597x, MVA = –5,362,722 RMB '000. Manual verification: 0.598x (0.001x gap from workbook shares_outstanding rounding — not a spec or LLM error).

---

### Gap 2 — Cash Coverage D&A Source Not Declared (GAP 1)

**Where it surfaced:** Structural risk present in v1 for all executors. iQIYI embeds all D&A in `INC_cost_goods_sold`; `INC_depreciation = 0` on the Income Statement. An executor reading the Cash Coverage formula without source guidance would look for D&A on the IS, find zero, and either compute Cash Coverage = TIE (0.25x) or attempt a COGS decomposition neither the spec nor the named-range model supports.

**What the spec caused:** The Cash Coverage formula (`(INC_ebit + D&A) / INC_interest_expense`) requires a D&A value, but the spec did not name `CASH_depreciation_amortization` as a model-specific range, did not disclose its source (CF Statement operating section), and did not note that `INC_depreciation = 0`. The LLM had no valid input for the numerator's D&A component.

**Exact spec language added (v2.0):**
- §4 Named Range Conventions: `| CASH_depreciation_amortization | D&A add-back from CF Statement operating section — NOTE: INC_depreciation = 0 because iQIYI embeds all D&A in INC_cost_goods_sold | 13,264,120 | RMB '000 |`

**Correct value in raw LLM output:** Cash Coverage = 14.83x, explicitly citing `CASH_depreciation_amortization = RMB 13,264.1M` sourced from "the operating section of the Cash Flow Statement." Manual verification: (229,315 + 13,264,120) / 909,616 = 14.83x ✓.

---

### Gap 3 — Du Pont Mismatch Internal Contradiction Between §7 and §9 (GAP 2)

**Where it surfaced:** Two separate occurrences. First: earlier Claude test reproduced the original wrong explanation (§7 v1 said "mismatch source: `currentYear_assets_total` vs. `startYear_equity`" — mixing an assets variable with an equity variable). Second: after Round 2 fixed §7, Gemini Test 5 re-triggered the same error by deferring to §9, which still contained the old wrong language.

**What the spec caused:** A two-section spec where §7 (validation table) and §9 (executor instructions) described the same concept with different language — and different models resolved the contradiction differently. Claude deferred to §7 (the validation table); Gemini deferred to §9 (the executor instruction). Neither resolution was logically wrong given the ambiguity. The internal contradiction was the root cause.

**This gap is significant beyond the error itself:** it demonstrates that a spec must be internally consistent across all sections, and that cross-vendor testing is the most reliable way to surface latent contradictions. Claude's consistent deference to §7 masked the §9 problem for four tests before Gemini exposed it.

**Exact spec language added (v2.0) — §9 rewritten to mirror §7:**
*"RATIO_leverage uses currentYear_equity (FY2025 year-end) as its denominator, while direct ROE uses startYear_equity (FY2024 year-end). This produces Du Pont ROE (–1.56%) ≠ direct ROE (–1.53%). Both formulations are internally consistent; the 3-basis-point difference is a methodological artifact, not a computation error. Do not treat as an error."*

**Correct execution in raw LLM output:** "Du Pont ROE (–1.56%) differs from direct ROE (–1.53%) by 3 basis points. RATIO_leverage uses currentYear_equity (FY2025 year-end) as its denominator, while direct ROE uses startYear_equity (FY2024 year-end). Both formulations are internally consistent; the difference is a methodological artifact, not a computation error." ✓

---

## Part C — Three Revisions for Next Time

**Revision 1 — Add a mandatory "Unit Conversion" pre-computation block before any multi-currency formula (addresses Gap 1 / GAP B)**

Any spec covering a company that reports in one currency but has market data in another (common for ADR-listed companies: iQIYI reports in RMB, trades in USD) should include a standalone pre-computation section before §6 ratio definitions. This section lists every unit conversion required, the formula, the computed value, and the unit of the output. No ratio formula in §6 should reference a raw input in a different unit than all other inputs to that formula. Rationale: the FX conversion gap was the single most consequential error in this project and is fully preventable by design.

**Revision 2 — Declare every model-specific named range with source annotation in §4 (addresses Gap 2 / GAP 1)**

The Stage 1 template named ranges are documented by convention. Model-specific ranges — ranges that exist in the workbook but not in the template (such as `CASH_depreciation_amortization`) — must be explicitly declared in §4 with: (a) the named range, (b) its definition, (c) its value, (d) its source (which tab, which line item), and (e) any disclosure about IS line items that are zero for accounting structure reasons. An executor should never need to infer a source — every input to every formula must be traceable to a declared range with a documented origin.

**Revision 3 — Implement a single-source-of-truth rule for any concept described in more than one section (addresses Gap 3 / GAP 2)**

When a spec describes the same concept in multiple sections (e.g., Du Pont mismatch in both §7 validation and §9 executor instructions), one section must be designated as authoritative and the other must cross-reference it rather than restate it independently. Example: §9 could read "See §7 validation note on Du Pont methodology — apply the explanation there verbatim." This prevents the §7/§9 contradiction pattern entirely, regardless of which LLM family executes the spec. The underlying principle: duplication in a spec is a latent contradiction waiting to be triggered.

---

## Part D — Effectiveness Rating

**Rating: 4 / 5**

**Justification:**

Spec v2.0 is effective: it produced a clean, verifiable analysis (`deliverables/2026-05-18-luong-iqiyi-llm-raw.md`) on the first v2.0 execution, passing all 10 identified gaps, with all ratios confirmed correct by manual recomputation (6 ratios, 5 exact matches, 1 workbook rounding gap traceable to a Stage 2–Stage 3 data update). The named-range framework prevented hallucination for all in-model data across all six test executions — the only hallucination risk was the FY2023 reference in v1, which was eliminated architecturally in Round 3.

The rating is 4, not 5, for two reasons. First, the three most consequential gaps (FX conversion, D&A source, Du Pont mismatch) were all detectable in Round 1 with careful pre-draft review — they reflect under-specification in the initial draft, not inherent difficulty. A stronger first draft would have caught them before testing and reduced iteration from 5 rounds to 2–3. Second, the §9 vs. §7 contradiction survived two full rounds of editing (Rounds 2 and 3) before being caught in Round 5 via cross-vendor testing — indicating the spec lacked a formal internal consistency review step during revision.

A rating of 5 would require the spec to have been clean enough on first test to require only minor refinements. The 5-round, 10-gap journey is evidence of a good HIL process, but also of a spec that needed more upfront precision.

---

## Part E — Forward Link

For any future multi-currency financial analysis spec: begin with a mandatory pre-computation block listing every unit conversion with formula and computed value — this single structural addition would have prevented the most critical gap in this project and applies to any ADR, cross-listed, or multinational analysis where market data and financial data are denominated differently.

---

## Part F — Retrospective Process Feedback

*(≤150 words — structural suggestion for the template itself)*

The section-by-section verdict format (Part A) effectively identifies *where* a spec failed, but the table has no column for *when* the gap was discovered — which round, and through what testing method (self-review, Claude test, Gemini cross-vendor test). Adding a "Discovery round" column would allow analysts and instructors to distinguish early-round structural problems (under-specified draft) from late-emerging edge cases (adequate for primary model, not vendor-agnostic). This distinction is meaningful for rubric assessment: a gap caught in Round 1 from a basic self-review suggests the spec was simply under-drafted; a gap caught in Round 5 only via cross-vendor testing suggests the spec was sound for its primary executor but not robustly generalized. The current template treats all gaps equivalently regardless of when they emerged — a timing column would add diagnostic depth without significant complexity.

*(Word count: 133)*
