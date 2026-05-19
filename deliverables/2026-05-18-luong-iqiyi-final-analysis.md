---
template: final-analysis
purpose: "Evaluated, annotated, and corrected version of the Stage 5 LLM output — adds manual verification, editorial corrections, executive voice, and LLM evaluation to the raw AI-generated ratio analysis"
audience: student
fields_required: [company_data_summary, ratio_results, dupont_analysis, strategic_recommendations, llm_evaluation, executive_justification]
naming_convention: "YYYY-MM-DD-{lastname}-{company-slug}-final-analysis.md"
course: BUS-629
stage: 5
company: "iQIYI Inc (IQ, NASDAQ)"
notes: "LLM raw output: deliverables/2026-05-18-luong-iqiyi-llm-raw.md (Claude claude-sonnet-4-6, v3 — first clean pass across all 10 spec gaps). Manual verification: analysis/validation/2026-05-18-luong-iqiyi-stage5-verification.md (6 ratios, 5 exact matches, 1 workbook rounding discrepancy). Spec retrospective: deliverables/2026-05-18-luong-iqiyi-spec-retrospective.md. Sections 5–6 (LLM Evaluation and Executive Justification) to be added after spec retrospective is complete."
---

# iQIYI Inc. (IQ, NASDAQ) — Evaluated Final Analysis

**FY2025 Accounting & Performance Ratio Analysis | BUS-629 Stage 5**

**Author:** Luong Duy Phuong
**Date:** May 18, 2026
**Course:** BUS-629 · University of Hawaiʻi at Mānoa, Shidler College of Business
**Source:** iQIYI Form 20-F FY2025, SEC EDGAR CIK 0001722608

> **Note on this document.** This is the evaluated, annotated version of the raw LLM output (`deliverables/2026-05-18-luong-iqiyi-llm-raw.md`). Ratio values have been manually verified against the Stage 3 workbook (`models/builds/2026-05-14-luong-iqiyi-financials.xlsx`); see `analysis/validation/2026-05-18-luong-iqiyi-stage5-verification.md` for the full verification table. Editorial annotations and corrections are integrated throughout. Sections 5 (LLM Evaluation) and 6 (Executive Justification) are completed separately following the spec retrospective.
> Spec retrospective: `deliverables/2026-05-18-luong-iqiyi-spec-retrospective.md`

---

## 1. Company & Data Summary

### 1.1 Company Context

iQIYI Inc. (Ticker: IQ, NASDAQ) is China's largest online video streaming platform, operating in movies, long-form drama, anime, and variety content. The company is incorporated in the Cayman Islands and operates in the People's Republic of China through a Variable Interest Entity (VIE) structure — a legally complex arrangement used by PRC-based companies listed on U.S. exchanges to work around foreign-ownership restrictions in the media sector.

The VIE structure carries material risk beyond what a standard leverage or coverage ratio captures: the legal enforceability of contractual arrangements between iQIYI's offshore holding company and its PRC operating entities has never been tested in Chinese courts, and the PRC government retains unilateral authority to invalidate these structures. This risk is embedded in the 3% China risk premium applied to the WACC assumption (see Section 1.3), but the premium may understate tail exposure in scenarios involving content regulation or cross-border data-security enforcement.

iQIYI files its annual report with the U.S. Securities and Exchange Commission as a **Form 20-F** under U.S. GAAP — the same standard as a domestic issuer's Form 10-K. This allows direct ratio computation without reconciliation to IFRS. The fiscal year ended December 31, 2025.

### 1.2 Financial Headline

FY2025 results represent a material deterioration relative to FY2024:

| Metric | FY2024 | FY2025 | Change |
|--------|--------|--------|--------|
| Revenue | — | RMB 27,291.3M | –6.6% YoY |
| Gross Margin | 24.9% | 21.1% | –380 bps |
| Net Margin | +2.7% | –0.75% | Sign reversal |
| Net Income | positive | –RMB 204.0M | Loss |
| Market Cap (USD) | — | ~$1.14B | –40.7% YTD |

The sign reversal in net margin — from +2.7% to –0.75% — in a single fiscal year is the primary analytical event of FY2025. The direct cause is not operating deterioration alone but the interaction of thin operating margin (1.75%) with a fixed interest burden (RMB 909.6M) that exceeds EBIT (RMB 229.3M), producing TIE of 0.25x and a net loss.

### 1.3 Assumptions and Accounting Treatment

All monetary values are in **RMB '000** unless otherwise noted. Key assumptions:

| Assumption | Value | Source |
|------------|-------|--------|
| `fx_rate` (RMB/USD) | 6.9931 | Federal Reserve H.10, Dec 31, 2025 |
| `cost_capital` (WACC) | 9.0% | Spec assumption; includes 3% China risk premium |
| `tax_rate` | 25.0% | PRC HNTE standard rate |
| `share_price` | $1.18 USD | Stage 3 workbook |
| `shares_outstanding` | 964,912 thousand | Stage 3 workbook (ADS basis) |

**Critical accounting note — D&A treatment:** iQIYI does not disclose depreciation and amortization as a separate line item on the Income Statement. All D&A is embedded in `INC_cost_goods_sold`. As a result, `INC_depreciation = 0` on the IS, and the Cash Coverage ratio cannot use an IS-derived D&A figure. The correct source is `CASH_depreciation_amortization = RMB 13,264.1M`, taken from the operating section of the Cash Flow Statement. This distinction is material: Cash Coverage (14.83x) and TIE (0.25x) diverge by a factor of nearly 60x precisely because D&A of RMB 13.3B is a non-cash charge embedded in COGS that depresses EBIT without consuming cash.

**FX conversion note — Market-to-Book and MVA:** `market_capitalization` is denominated in USD '000 (share_price × shares_outstanding). All Balance Sheet figures are in RMB '000. Dividing directly would produce a dimensionless ratio with no financial meaning. The required conversion: `market_capitalization_rmb = market_capitalization × fx_rate` = 1,138,596 × 6.9931 = **7,962,317 RMB '000**. All Performance ratio formulas use `market_capitalization_rmb`, not the raw USD figure.

> **Data note:** The Stage 2 memo (May 14, 2026) recorded `shares_outstanding = 962,959K ADS`; the Stage 3 workbook (used for all computations here) records `964,912K ADS`. The 1,953K share difference — likely due to a balance date or source update between Stage 2 and Stage 3 data entry — produces a `market_cap_usd` of 1,138,596 USD '000 vs. the spec's stated 1,136,292 USD '000, and a Market-to-Book of 0.598x vs. the LLM's 0.597x. The difference is 0.001x and does not affect any analytical conclusion. The workbook figure is used throughout this analysis as the Stage 3-authoritative source.

### 1.4 Scope

This analysis covers **FY2025 standalone performance** compared where relevant against **FY2024** as the prior-year benchmark. No FY2023 data are used — the three-year trend analysis was explicitly removed from scope in Stage 4 (architectural decision, Round 3) because the Stage 3 model does not include FY2023 named ranges, making any FY2023 ratio derivation an out-of-model back-calculation. Income Statement absolute values for FY2024 are not derived from the named-range model; only FY2024 percentage metrics (gross margin 24.9%, net margin +2.7%) sourced directly from the 20-F are cited.

---

## 2. Ratio Results & Interpretation

### 2.1 Performance Ratios

iQIYI's performance ratios confirm that the firm is destroying economic value at both the market and accounting levels.

**Market Value Added (MVA) = –RMB 5,362.7M** (`market_capitalization_rmb` – `currentYear_equity` = 7,962.3M – 13,308.9M). The negative MVA means the capital markets assign RMB 5.4B *less* value to the firm's equity than its balance-sheet carrying amount. This is not merely a valuation discount — it is a structural signal. The market is pricing in the probability that iQIYI's future cash flows will not recover the equity base, a rational judgment given the –RMB 42.7B accumulated retained-earnings deficit that overwhelms RMB 56.0B of paid-in capital to produce positive book equity of only RMB 13.3B.

> **Verification note:** The manual Market-to-Book computation (Section 2 of the verification table) yields 0.598x vs. the LLM's 0.597x — a 0.001x gap attributable entirely to the shares_outstanding input difference between the workbook (964,912K) and the spec (implied 963,044K), not a formula or logic error. The LLM's FX conversion methodology is correct.

**Market-to-Book = 0.597x** (LLM) / **0.598x** (manual, workbook-authoritative). Either figure confirms the market prices iQIYI equity at a ~40% discount to reported net asset value. The 40% discount is consistent with two compounding factors: (1) the accumulated deficit makes book equity fragile — any further net loss directly erodes the RMB 13.3B cushion; and (2) the VIE structure introduces a legal-enforceability premium that discounts the practical recoverability of the asset base.

**EVA = –RMB 1,570.3M** (`NOPAT` – `cost_capital × startYear_total_capitalization` = 478.2M – 2,048.5M). NOPAT of RMB 478.2M represents positive after-tax operating returns — iQIYI does generate value at the operating level. However, the capital charge of RMB 2.0B, applied to RMB 22.8B of total capitalization at a 9% WACC, far exceeds operating returns. Approximately 77% of the EVA gap is the capital charge, not zero operating income. The implication: even meaningful EBIT growth would not close the EVA gap without simultaneous deleveraging or WACC reduction. The 3% China risk premium embedded in the 9% WACC is doing significant damage here — a WACC of 6% (removing the premium) would reduce the capital charge by RMB 683M and bring EVA closer to breakeven.

### 2.2 Profitability Ratios

The ROA–ROE divergence is the central profitability story of FY2025.

**ROA = 1.04%** (`NOPAT / startYear_total_assets` = 478.2M / 45,760.5M). ROA is positive because NOPAT adds back the after-tax cost of debt to net income, measuring operating returns independently of the capital structure. The business generates RMB 1.04 of after-tax operating income per RMB 100 of assets — modest, but positive. *Manual verification confirms: 478,169 / 45,760,525 = 1.0449% ≈ 1.04%. ✓*

**ROE = –1.53%** (`INC_net / startYear_equity` = –204.0M / 13,373.8M). The 255-basis-point gap between ROA (positive) and ROE (negative) is entirely explained by the capital structure: `INC_interest_expense` of RMB 909.6M transforms a positive operating result into a net loss. This is the classic over-leverage effect — the firm's assets earn more than cost-of-debt on an after-tax operating basis, but the nominal interest payment overwhelms EBIT at 0.25x coverage.

**Gross margin compression from 24.9% (FY2024) to 21.1% (FY2025)** — a 380 bps deterioration — is the upstream driver of the entire profitability collapse. `INC_cost_goods_sold` of RMB 21,542.3M on revenue of RMB 27,291.3M leaves gross profit of only RMB 5,748.9M, barely covering `INC_sga` of RMB 5,519.6M. The resulting operating profit margin of 1.75% provides no buffer for adverse shocks.

The bridge from operating margin (1.75%) to net margin (–0.75%) runs: EBIT RMB 229.3M → minus interest RMB 909.6M → plus other income RMB 620.8M → pre-tax loss RMB 59.5M → minus tax RMB 144.5M → **net loss RMB 204.0M**. The tax charge on a pre-tax loss is not an error — it reflects the tax expense on profitable subsidiaries within the consolidated group even when the consolidated entity is loss-making.

### 2.3 Efficiency Ratios

**Asset Turnover = 0.596x** (`INC_sales / startYear_total_assets` = 27,291.3M / 45,760.5M). The ratio reflects iQIYI's capital-intensive content model: RMB 45.8B of assets generate only RMB 27.3B in revenue. The content library is largely fixed-cost and long-duration — assets do not shrink proportionally when revenue falls, so declining revenue mechanically compresses the turnover ratio. This is the Du Pont channel through which content cost structure affects ROE.

**Inventory Turnover = 55.4x / Days in Inventory = 6.6 days.** These figures appear anomalously high for a media company but are economically rational. `BAL_inventories` represents licensed copyrights carried at amortized cost on the balance sheet. These are consumed through COGS as content is delivered, generating high turnover velocity relative to the carrying balance. The metric does not capture supply-chain efficiency in the traditional manufacturing sense; it reflects content recognition accounting policy under U.S. GAAP.

**Average Collection Period = 29.3 days** (`startYear_receivables / daily_sales_average`). Operationally reasonable for a platform collecting subscription and advertising revenues. However, in the context of an advertising market downturn affecting PRC digital media in FY2025, the receivables quality warrants scrutiny. Advertising receivables carry higher counterparty default risk than subscription revenues, and a 29-day cycle provides limited buffer if advertiser credit conditions tighten further in FY2026.

### 2.4 Leverage Ratios

**Times Interest Earned (TIE) = 0.25x** (`INC_ebit / INC_interest_expense` = 229.3M / 909.6M). This is the single most urgent risk signal in iQIYI's FY2025 financials. A TIE below 1.0x means the firm cannot service interest obligations from operating income alone — it is structurally dependent on non-operating income (RMB 620.8M in FY2025) to bridge the gap. If non-operating income — which includes investment gains and other non-recurring items — declines, the firm faces a direct path to covenant violation.

**Cash Coverage = 14.83x** (`(INC_ebit + CASH_depreciation_amortization) / INC_interest_expense` = 13,493.4M / 909.6M). The divergence between TIE (0.25x) and Cash Coverage (14.83x) is not a paradox — it reflects the D&A accounting structure described in Section 1.3. Adding back RMB 13.3B of non-cash content amortization to EBIT reveals that iQIYI's *cash generation capacity* is substantially higher than its accrual-based interest coverage. *Manual verification confirms: (229,315 + 13,264,120) / 909,616 = 14.83x. ✓*

The correct interpretation: TIE understates cash-based debt service capacity; Cash Coverage overstates it to the extent that D&A represents the real economic cost of content replenishment — a cost that must be incurred in future periods to maintain the content library and sustain revenue. Neither ratio alone is the complete picture.

**Total Debt Ratio = 71.5%** (`currentYear_liabilities_total / currentYear_assets_total` = 33,372.8M / 46,681.7M). Creditors finance 71.5% of the asset base. Combined with Long-Term Debt-Equity of 0.757x and Long-Term Debt Ratio of 43.1%, the picture is one of structural over-leverage concentrated in USD-denominated convertible notes — PAG Notes (USD 550M at 6%) and 2028 Notes (USD 208M at 6.5%) — serviced from primarily RMB operating cash flows.

### 2.5 Liquidity Ratios

**Current Ratio = 0.47x** (`BAL_assets_current / BAL_liabilities_current` = 10,290.3M / 22,067.3M). *Manual verification confirms: 10,290,295 / 22,067,307 = 0.4663 ≈ 0.47x. ✓*

**NWC = –RMB 11.8B.** The negative NWC requires careful interpretation in the context of a subscription streaming platform. A significant portion of `BAL_liabilities_current` consists of **deferred subscription revenue** — cash already collected from subscribers for content not yet delivered. This liability is discharged by delivering content, not by cash outflow. It is therefore qualitatively different from trade payables or debt maturities. Stripping deferred revenue from current liabilities would produce a materially less severe NWC figure.

That said, the adjusted liquidity position is not benign. `BAL_debt_short_term_curr` of RMB 4,690.6M represents near-term debt maturities that require cash or refinancing — not content delivery. With `Cash Ratio = 0.20x` (cash covering only 20% of current liabilities), and the total cash balance of RMB 4,354.3M essentially matched one-for-one by short-term debt maturities (RMB 4,690.6M), the firm has zero liquidity buffer for operational disruptions. The ability to meet near-term obligations is contingent on subscriber cash inflow continuity and debt rollover execution.

---

## 3. Du Pont Analysis

The four-factor Du Pont model decomposes ROE into its operational and financial drivers, isolating the precise source of equity return destruction.

| Factor | Named Range | Value | Interpretation |
|--------|-------------|-------|----------------|
| Leverage | `RATIO_leverage` | 3.508x | High asset base relative to equity |
| Asset Turnover | `RATIO_asset_turnover` | 0.596x | Low revenue yield per RMB of assets |
| Operating Profit Margin | `RATIO_operating_profit_margin` | 1.75% | Thin margin after content costs |
| Debt Burden | `RATIO_debt_burden` | –0.427x | Interest expense drives net loss — **sign flip** |
| **ROE (Du Pont)** | | **–1.56%** | Negative ROE driven by Debt Burden |

*Manual verification confirms Du Pont ROE: 3.508 × 0.596 × 0.01752 × (–0.427) = –1.564% ≈ –1.56%. ✓*

**Factor 1 — Debt Burden (–0.427x): the sign-flip mechanism.** `RATIO_debt_burden = INC_net / NOPAT` = –204.0M / 478.2M. The negative sign is the mathematical mechanism by which ROE becomes negative despite positive Leverage, Asset Turnover, and Operating Profit Margin. The business generates positive after-tax operating income (RMB 478.2M), but interest expense of RMB 909.6M exceeds EBIT of RMB 229.3M, making net income negative. Every other factor in the Du Pont chain is positive — the entire value destruction originates in this single ratio.

**Factor 2 — Leverage (3.508x): the amplifier.** Under normal conditions — when Debt Burden is positive — leverage amplifies ROE upward. In iQIYI's case, leverage amplifies the loss: a higher asset-to-equity ratio magnifies the destructive effect of the negative Debt Burden. This is the reverse-leverage effect intrinsic to overleveraged firms with sub-1.0x interest coverage. Reducing leverage (through asset disposal, equity issuance, or debt repurchase) would reduce the amplification of losses before Debt Burden turns positive.

**Factor 3 — Asset Turnover (0.596x): the structural constraint.** Revenue of RMB 27.3B supported by assets of RMB 45.8B (prior year base) produces below-average turnover for a technology platform. The content library is predominantly long-duration and illiquid — revenue shortfalls do not produce proportional asset reductions. Improving this factor requires growing revenue without proportional asset growth, a challenging condition in a declining-revenue environment.

**Factor 4 — Operating Profit Margin (1.75%): the thin buffer.** `RATIO_operating_profit_margin = NOPAT / INC_sales` = 478.2M / 27,291.3M. The 1.75% margin leaves essentially no operating buffer. Any further gross margin compression (380 bps already occurred in FY2025) or SG&A increase would eliminate the positive contribution from this factor and push the Du Pont product more deeply negative.

**Methodological Note — Du Pont ROE (–1.56%) vs. Direct ROE (–1.53%):** The 3-basis-point difference is a methodological artifact, not a computation error. `RATIO_leverage` uses `currentYear_equity` (FY2025 year-end balance sheet) as its denominator, while direct ROE uses `startYear_equity` (FY2024 year-end). Both formulations are internally consistent with their respective conventions; they produce slightly different equity denominators (RMB 13,308.9M vs. RMB 13,373.8M), resulting in the 3 bp gap. Neither figure is "correct" or "incorrect" in isolation — the choice of convention must be disclosed and applied consistently.

**Du Pont Conclusion:** Restoring positive ROE requires resolving the Debt Burden factor first — either by growing `INC_ebit` past the interest expense threshold (requires >RMB 909.6M EBIT, i.e., 4× current EBIT) or by reducing `INC_interest_expense` through debt repurchase or refinancing. Once Debt Burden turns positive, the existing Leverage ratio (3.508x) will amplify positive returns. Asset Turnover and Operating Profit Margin improvements are secondary priorities, but upstream gross margin recovery is the prerequisite for any EBIT expansion.

---

## 4. Strategic Recommendations

The LLM identified five strategically sound recommendations. The assessment below confirms factual accuracy, evaluates analytical depth, and identifies where my own judgment adds to or modifies the LLM's framing.

### Recommendation 1: Prioritize Debt Restructuring (TIE = 0.25x)

**LLM's recommendation:** Accelerate repurchase of PAG Notes (USD 550M at 6%) ahead of 2028 maturity; alternatively, refinance USD-denominated notes into lower-rate RMB instruments to reduce both interest expense and currency mismatch. Maintain minimum liquidity covenants post-repurchase.

**Assessment — Analytically correct, but incomplete on execution risk.** The LLM correctly identifies TIE as the primary risk and links it to the Du Pont Debt Burden factor (–0.427x). The arithmetic is sound: reducing `INC_interest_expense` from RMB 909.6M toward RMB 229.3M (EBIT level) would move Debt Burden from –0.427x toward 1.0x, reversing the ROE sign. However, the LLM does not address the **refinancing paradox**: at a TIE of 0.25x, iQIYI's credit quality is severely impaired. Accessing new debt at lower rates may require credit enhancement (asset pledges, Baidu parent guarantee) or a covenant waiver — none of which are in the LLM's analysis. The debt restructuring recommendation is correct in direction but optimistic on execution feasibility.

**My addition:** The more actionable near-term lever is **suspension of capital expenditure** to preserve cash for debt service while management evaluates refinancing. The LLM mentions deploying RMB 4.35B cash toward buybacks but does not weigh this against the immediate debt maturity of RMB 4.69B (BAL_debt_short_term_curr) — which essentially equals the full cash balance. There is no surplus cash for discretionary buybacks.

### Recommendation 2: Content Cost Discipline — Gross Margin Recovery (21.1% vs. 24.9%)

**LLM's recommendation:** Shift content mix toward lower-cost originals and catalogue content; renegotiate or allow high-cost exclusive licenses to expire; implement return-on-content analytics. Each 100 bps gross margin recovery ≈ RMB 273M incremental gross profit.

**Assessment — Correct framing, strong quantification.** The RMB 273M per 100 bps estimate is arithmetically consistent with revenue of RMB 27.3B (0.01 × 27,291 = 272.9M). The LLM correctly identifies this as the upstream driver — gross margin compression from 24.9% to 21.1% is the event that leaves SG&A consuming virtually all gross profit. The LLM also correctly flags the content-quality trade-off risk: cost reduction risks subscriber churn, which would worsen all revenue-denominated ratios.

**My addition:** The LLM does not differentiate between membership revenue (relatively sticky) and advertising revenue (cyclical, PRC-macro-sensitive). Gross margin recovery through content cost cuts will disproportionately affect advertising-driven content (high-cost reality shows, live events), which is already under pressure from the PRC advertising downturn. Targeting content cost cuts at ad-supported content tiers first would protect subscription gross margin while reducing the most economically exposed cost bucket.

### Recommendation 3: Improve Asset Productivity (Asset Turnover = 0.596x)

**LLM's recommendation:** Grow subscription ARPU through premium tier pricing; monetize content library through B2B licensing; selectively dispose of underperforming content assets. A 0.1x improvement in Asset Turnover ≈ RMB 4.6B incremental revenue on a stable asset base.

**Assessment — Direction correct, magnitude assumption contestable.** The RMB 4.6B revenue estimate assumes the asset base remains at RMB 45.8B while turnover improves from 0.596x to 0.696x — a simplification that ignores the asset growth required to sustain content quality. In practice, growing subscription ARPU in the PRC market faces competitive pressure from ByteDance (Xigua Video), Tencent Video, and iQIYI's own free-tier cannibalization. The LLM notes PRC content licensing regulations as a constraint but does not quantify the revenue upside probability, leaving the 0.1x improvement target as an aspiration without a credible pathway.

**My addition:** B2B licensing of original productions (co-productions with international distributors, licensing to Southeast Asian platforms) is the most credible lever the LLM identifies — it generates incremental revenue from already-amortized content assets with minimal incremental cost, directly improving Asset Turnover without proportional COGS growth.

### Recommendation 4: Restructure Working Capital (Current Ratio = 0.47x, NWC = –RMB 11.8B)

**LLM's recommendation:** Extend maturity profile of short-term debt by refinancing short-duration tranches into longer-tenor instruments before they enter current liabilities. Accelerate subscriber cash collection through annual prepaid plans to improve cash inflows.

**Assessment — Partially correct; the deferred revenue interpretation requires nuance.** The LLM correctly identifies `BAL_debt_short_term_curr` of RMB 4.7B as the genuine near-term cash risk within current liabilities. The annual prepaid subscription strategy is sound — upfront subscription cash reduces Days Sales Outstanding and improves the cash conversion cycle.

**My addition:** The LLM does not explicitly distinguish between the *structural* NWC deficit (driven by deferred subscription revenue, which is self-liquidating) and the *financial* NWC deficit (driven by maturing debt, which requires cash). Conflating the two overstates the severity of the working capital problem in operational terms and understates the severity in financial terms. Management communication to investors should separately disclose the debt-maturity component of current liabilities to avoid the current ratio (0.47x) being read as an imminent operational insolvency signal.

### Recommendation 5: Mitigate VIE/Currency/Regulatory Risk

**LLM's recommendation:** Implement cross-currency interest rate swaps on USD note exposure; establish formal WACC reassessment triggers for (a) RMB/USD movement >5%, (b) HNTE license non-renewal, (c) PRC content regulatory action affecting >10% of content library.

**Assessment — Analytically sophisticated, operationally constrained.** The three WACC reassessment triggers are concrete and well-designed. The currency hedging recommendation is directionally sound — USD 758M of principal (PAG + 2028 Notes) against RMB operating cash flows is a structural mismatch.

**My addition:** The LLM does not address the **HNTE tax rate expiration risk** identified in the Stage 2 memo. Several key PRC subsidiaries qualify for a preferential HNTE tax rate of 15% (vs. standard 25%) that expires between 2026–2028 and requires active renewal. If HNTE status is not renewed, the effective consolidated tax rate would increase, further compressing NOPAT and worsening EVA (already –RMB 1,570.3M). This should be Recommendation 5's primary analytical focus — not hedging instruments, which carry their own basis risk and may be constrained by PRC capital account restrictions. The tax rate risk is more certain, more quantifiable, and more immediate than the FX swap recommendation.

---

*Sections 5 (LLM Evaluation & Annotations) and 6 (Executive Justification) will be added following completion of the spec retrospective (`deliverables/2026-05-18-luong-iqiyi-spec-retrospective.md`).*
