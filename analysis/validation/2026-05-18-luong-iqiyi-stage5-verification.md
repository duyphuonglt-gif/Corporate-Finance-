---
template: verification-table
purpose: "Manual recomputation of ≥5 ratios from Stage 3 workbook values, compared against Stage 5 LLM output — satisfies Stage 5 rubric criterion: Manual verification artifact"
audience: student
fields_required: [assumptions, verification_table, discrepancy_notes]
naming_convention: "YYYY-MM-DD-{lastname}-{company-slug}-stage5-verification.md"
course: BUS-629
stage: 5
company: "iQIYI Inc (IQ, NASDAQ)"
notes: "Source data: models/builds/2026-05-14-luong-iqiyi-financials.xlsx (Stage 3 workbook). LLM output compared against: deliverables/2026-05-18-luong-iqiyi-llm-raw.md (v3, Claude claude-sonnet-4-6). Six ratios recomputed across five categories: Performance (2), Profitability (1), Leverage (1), Du Pont (1), Liquidity (1). Five match exactly; one shows a 0.001x discrepancy traced to shares_outstanding rounding between workbook and spec."
---

# Stage 5 Manual Verification Table — iQIYI Inc (IQ)

**Author:** Luong Duy Phuong
**Course:** BUS-629 · University of Hawaiʻi at Mānoa, Shidler College of Business
**Source data:** `models/builds/2026-05-14-luong-iqiyi-financials.xlsx`
**LLM output:** `deliverables/2026-05-18-luong-iqiyi-llm-raw.md`

---

## Assumptions and Derived Inputs

All monetary values in RMB '000 unless noted. Tax rate = 25% (PRC HNTE standard rate, consistent with spec).

| Named Range | Value | Unit | Source |
|-------------|-------|------|--------|
| `BAL_assets_total` FY2025 | 46,681,735 | RMB '000 | Stage 3 workbook |
| `BAL_assets_total` FY2024 (`startYear_total_assets`) | 45,760,525 | RMB '000 | Stage 3 workbook |
| `BAL_equity_total` FY2025 (`currentYear_equity`) | 13,308,926 | RMB '000 | Stage 3 workbook |
| `BAL_equity_total` FY2024 (`startYear_equity`) | 13,373,764 | RMB '000 | Stage 3 workbook |
| `BAL_assets_current` FY2025 | 10,290,295 | RMB '000 | Stage 3 workbook |
| `BAL_liabilities_current` FY2025 | 22,067,307 | RMB '000 | Stage 3 workbook |
| `startYear_total_capitalization` (FY2024) | 22,761,169 | RMB '000 | Stage 3 workbook |
| `INC_net` FY2025 | –204,043 | RMB '000 | Stage 3 workbook |
| `INC_ebit` FY2025 | 229,315 | RMB '000 | Stage 3 workbook |
| `INC_interest_expense` FY2025 | 909,616 | RMB '000 | Stage 3 workbook |
| `INC_sales` FY2025 | 27,291,300 | RMB '000 | Stage 3 workbook |
| `CASH_depreciation_amortization` FY2025 | 13,264,120 | RMB '000 | Stage 3 workbook (CF Statement) |
| `share_price` | 1.18 | USD | Stage 3 workbook |
| `shares_outstanding` | 964,912 | thousands | Stage 3 workbook |
| `fx_rate` | 6.9931 | RMB/USD | Federal Reserve H.10, Dec 31, 2025 |
| `cost_capital` | 9% | — | Spec assumption |
| `tax_rate` | 25% | — | PRC HNTE rate (inferred: consistent with LLM's NOPAT = 478.2M) |

**NOPAT (derived):**
`INC_net + (1 − tax_rate) × INC_interest_expense`
= –204,043 + 0.75 × 909,616 = –204,043 + 682,212 = **478,169 RMB '000**

---

## Verification Table

| Ratio | Formula (named-range notation) | Manual value (arithmetic) | LLM's value | Match? | One-line note |
|-------|-------------------------------|--------------------------|-------------|--------|---------------|
| **Market-to-Book** | `market_capitalization_rmb / currentYear_equity` | Step 1: market_cap_usd = 1.18 × 964,912 = 1,138,596 USD '000 · Step 2: market_cap_rmb = 1,138,596 × 6.9931 = 7,962,317 RMB '000 · Step 3: 7,962,317 / 13,308,926 = **0.598x** | 0.597x | ✗ | 0.001x gap — workbook shares_outstanding (964,912K) implies market_cap 2,304 USD '000 higher than spec's stated 1,136,292; difference is ~1,869K shares, likely ADS rounding at source date |
| **Cash Coverage** | `(INC_ebit + CASH_depreciation_amortization) / INC_interest_expense` | (229,315 + 13,264,120) / 909,616 = 13,493,435 / 909,616 = **14.83x** | 14.83x | ✓ | LLM correctly sourced D&A from CF Statement (CASH_depreciation_amortization), not IS (INC_depreciation = 0) |
| **ROA** | `NOPAT / startYear_total_assets` | 478,169 / 45,760,525 = **1.04%** | 1.04% | ✓ | LLM correctly used FY2024 year-end (start-of-year) total assets as denominator, not FY2025 |
| **EVA** | `NOPAT − (cost_capital × startYear_total_capitalization)` | Capital charge = 0.09 × 22,761,169 = 2,048,505 · EVA = 478,169 − 2,048,505 = **–1,570,336 RMB '000** | –1,570,336 RMB '000 | ✓ | LLM correctly used startYear_total_capitalization (FY2024 LT debt + equity) as the capital base |
| **Du Pont ROE** | `RATIO_leverage × RATIO_asset_turnover × RATIO_operating_profit_margin × RATIO_debt_burden` | Lev = 46,681,735 / 13,308,926 = 3.508x · AT = 27,291,300 / 45,760,525 = 0.596x · OPM = 478,169 / 27,291,300 = 1.752% · DB = –204,043 / 478,169 = –0.427x · ROE = 3.508 × 0.596 × 0.01752 × (–0.427) = **–1.56%** | –1.56% | ✓ | All four factors match; LLM correctly used currentYear_equity for Leverage and startYear_assets for Asset Turnover |
| **Current Ratio** | `BAL_assets_current / BAL_liabilities_current` | 10,290,295 / 22,067,307 = **0.47x** | 0.47x | ✓ | Baseline liquidity check — exact match |

---

## Discrepancy Note — Market-to-Book (0.598x vs. 0.597x)

The 0.001x gap is not a LLM hallucination or spec gap. It is a **source-data rounding artifact** in shares outstanding:

- Stage 3 workbook: `shares_outstanding` = 964,912 thousand → `market_cap_usd` = 1.18 × 964,912 = **1,138,596 USD '000**
- Spec's stated value: `market_capitalization` = **1,136,292 USD '000** (implies shares = 963,044 thousand)
- Difference: 1,868 thousand ADS (~0.19%)

The spec likely sourced shares outstanding from a slightly different reporting date or used a rounded ADS figure. Both computations apply the FX conversion correctly (`market_cap_rmb = market_cap_usd × fx_rate`). The methodological approach is identical; the discrepancy is entirely in the input data, not the formula or logic.

**Verdict:** The LLM executed the FX conversion step correctly. The 0.001x difference reflects a minor workbook-vs-spec data source discrepancy, not an LLM error.
