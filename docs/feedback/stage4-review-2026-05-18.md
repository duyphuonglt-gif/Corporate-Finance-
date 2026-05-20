# Stage 4 review — 2026-05-18

Reviewing the Stage 4 spec at `docs/specs/2026-05-16-luong-iqiyi-spec.md`

## Section coverage

| Section | Present | Word count |
|---|---|---|
| 1. Scope & Objective | ✓ | 179 |
| 2. Model Architecture | ✓ | 136 |
| 3. Data Inputs | ✓ | 633 |
| 4. Named Range Conventions | ✓ | 190 |
| 5. Derived Inputs | ✓ | 195 |
| 6. Ratio Definitions & Formulas | ✓ | 391 |
| 7. Validation Rules | ✓ | 148 |
| 8. Analysis Requirements (Part B) | ✓ | 545 |
| 9. Du Pont Decomposition (Part B) | ✓ | 221 |
| 10. Strategic Recommendations (Part B) | ✓ | 202 |
| 11. Output Format (Part B) | ✓ | 344 |

## Observations

- Spec length: **3383 words** (brief targets 3–5 pages, ~1,500–2,500 words).
- Named-range notation usage: **384 hit(s)** across `BAL_*`, `INC_*`, `CASH_*`, `RATIO_*`, `startYear_*`, `currentYear_*`, `avg_*`.
- Ratio categories detected in Section 6: **performance, profitability, efficiency, leverage, liquidity, du pont** (6/6).
- Ratio table rows in Section 6: **30**.
- Validation rules counted in Section 7: **7**.
- Prompt log: **1635 words**, 0 explicit prompt block(s); HIL signals: 5 strong, 0 weak.

### Kindly-worded suggestions for improvement

**Stage 4 rubric notes**

- Strong submission — Parts A and B both fully developed, named-range notation used consistently, and visible HIL iteration on the prompt log. Stage 5 builds directly on this — feed *only* the spec to the LLM at Stage 5, verify five ratios by hand, and the deliverable falls out the other side.

**Looking ahead to Stage 5**

- **Stage 5 — LLM analysis + manual verification.** Run your Stage 4 spec through the LLM of your choice, then verify at least five of its ratio outputs against the workbook by hand. The polish rubric grades how cleanly the prior four stages tie together as a single deliverable, so revisit your earlier files with fresh eyes.


*This review is feedback-only — no scores included.* Score numbers live in the internal grade report and the instructor's email; this file is intended for review against your repo state.
