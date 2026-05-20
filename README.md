# BUS-629 Corporate Finance — iQIYI Inc (IQ, NASDAQ)

**Luong Duy Phuong · University of Hawaiʻi at Mānoa, Shidler College of Business · VEMBA 2026**

This repository contains a complete, end-to-end corporate finance analysis of iQIYI Inc (IQ, NASDAQ ADR) conducted across five stages of BUS-629 International Corporate Finance. The work covers company selection, financial modeling from Form 20-F, ratio analysis via a structured LLM specification, and a full AI-evaluated final analysis — built in the open as a professional portfolio artifact. Each stage is documented with source files, a prompt log of AI sessions, and iteration evidence so the analytical process is reproducible and auditable.

---

## What You'll Find Here

A structured analysis of iQIYI's FY2025 financial position, covering 30 accounting and performance ratios across six categories (Performance, Profitability, Efficiency, Leverage, Liquidity, Du Pont). The most significant findings: TIE of 0.25x signals structural debt service risk; Market-to-Book of 0.597x reflects a 40% equity discount; and the TIE-to-Cash-Coverage gap (0.25x vs. 14.83x) reveals large non-cash D&A — evidence of sustained capital injection by Baidu, iQIYI's controlling shareholder — that defers but does not resolve the insolvency risk. The executive verdict is: do not invest until iQIYI demonstrates content-driven user growth that independently services its USD-denominated debt.

---

## Project Status

| Stage | Deliverable | Key File | Commit |
|-------|------------|----------|--------|
| Stage 0 | Resume & Bio | `RESUME.md`, `BIO.md` | `e4c3ad3` |
| Stage 1 | Ratio template | `models/templates/performance-ratios-template.xlsx` | `81a8927` |
| Stage 2 | Company selection memo | `docs/decisions/2026-05-14-luong-iqiyi-selection.md` | `9ca751a` |
| Stage 3 | Financial model (FY2025 20-F) | `models/builds/2026-05-14-luong-iqiyi-financials.xlsx` | `fc13efd` |
| Stage 4 | LLM spec (v2.0, 10 gaps closed) | `docs/specs/2026-05-16-luong-iqiyi-spec.md` | `f6bc258` |
| Stage 5 | Final analysis + verification | `deliverables/2026-05-18-luong-iqiyi-final-analysis.md` | `d7135ec` |

---

## Repository Structure

```
Corporate-Finance/
├── README.md                    ← You are here
├── RESUME.md                    ← Author resume (Stage 0)
├── BIO.md                       ← Author bio (Stage 0)
├── LICENSE                      ← MIT
│
├── docs/
│   ├── decisions/
│   │   └── 2026-05-14-luong-iqiyi-selection.md     ← Stage 2 memo
│   └── specs/
│       └── 2026-05-16-luong-iqiyi-spec.md           ← Stage 4 LLM spec (v2.0)
│
├── models/
│   ├── templates/
│   │   └── performance-ratios-template.xlsx         ← Stage 1 template (unmodified)
│   └── builds/
│       └── 2026-05-14-luong-iqiyi-financials.xlsx   ← Stage 3 workbook
│
├── data/
│   ├── 20-F IQIYI.pdf                               ← Source: SEC EDGAR CIK 0001722608
│   └── mergent market IQ.pdf                        ← Secondary: Mergent by FTSE Russell
│
├── analysis/
│   └── validation/
│       ├── 2026-05-16-luong-iqiyi-stage4-iteration.md  ← Stage 4 HIL iteration log
│       └── 2026-05-18-luong-iqiyi-stage5-verification.md ← Stage 5 manual verification
│
└── deliverables/
    ├── prompt-log.md                                ← All AI sessions logged
    ├── 2026-05-18-luong-iqiyi-llm-raw.md            ← Stage 5 raw LLM output
    ├── 2026-05-18-luong-iqiyi-final-analysis.md     ← Stage 5 evaluated final analysis
    └── 2026-05-18-luong-iqiyi-spec-retrospective.md ← Stage 5 spec retrospective
```

---

## Key Findings — iQIYI FY2025

| Ratio | Value | Signal |
|-------|-------|--------|
| Times Interest Earned (TIE) | 0.25x | ⚠️ EBIT covers only 25% of interest — structural solvency risk |
| Cash Coverage | 14.83x | D&A of RMB 13.3B embedded in COGS; positive cash capacity |
| Market-to-Book | 0.597x | Market prices equity at 40% discount to book value |
| MVA | –RMB 5,362.7M | Capital markets assign less value than reported equity |
| EVA | –RMB 1,570.3M | Operating returns fall short of 9% WACC capital charge |
| ROE | –1.53% | Debt burden factor (–0.427x) flips positive operating returns negative |
| Gross Margin | 21.1% | –380 bps YoY — upstream driver of all profitability deterioration |
| Current Ratio | 0.47x | Structurally negative NWC; short-term debt maturities = cash balance |

---

## Source & Methodology

**Primary source:** iQIYI Inc. Form 20-F FY2025, SEC EDGAR, CIK 0001722608. Audited by Deloitte under U.S. GAAP. All figures in RMB thousands unless noted. FX rate: 6.9931 RMB/USD (Federal Reserve H.10, December 31, 2025).

**AI methodology:** Stage 4 produced a named-range technical specification that was executed by Claude (claude-sonnet-4-6) with no additional context. The spec underwent 5 rounds of HIL (human-in-the-loop) iteration and 6 independent reconstruction tests across two LLM families (Claude + Gemini) before reaching a clean pass on all 10 identified gaps. Stage 5 manually verified 6 ratios against the Stage 3 workbook and evaluated the LLM output for spec gaps vs. LLM limitations.

---

## Author

**Luong Duy Phuong** — Chief Technology Officer · VEMBA 2026, University of Hawaiʻi at Mānoa
