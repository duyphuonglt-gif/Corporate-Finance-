---
template: feedback-response
purpose: "Point-by-point response to instructor Stage 2 review — documents how each of the six suggestions was incorporated into the revised memo"
audience: student
fields_required: [source, observation, disposition, action_taken]
naming_convention: "YYYY-MM-DD-{lastname}-stage2-feedback-response.md"
course: BUS-629
stage: 2
company: "iQIYI Inc (IQ, NASDAQ)"
source_feedback: "docs/feedback/stage2-review (PR comments, instructor regrade confirmed all six items addressed)"
---

# Stage 2 Feedback Response — iQIYI Inc (IQ)

**Author:** Luong Duy Phuong
**Course:** BUS-629 · University of Hawaiʻi at Mānoa, Shidler College of Business
**Memo evaluated:** `docs/decisions/2026-05-14-luong-iqiyi-selection.md`
**Regrade verdict:** "Items addressed: all six suggestions from the original entry. Items still open: none material for the rubric."

---

## Summary

The instructor's Stage 2 review identified six structural and form issues — none analytical. All six were incorporated in the revised memo. The regrade confirmed zero open items. This document records what was changed and why, for Stage 5 feedback-incorporation visibility.

---

## Point-by-Point Response

### Suggestion 1 — Rename sections to match Stage 2 spec exactly (six numbered headings)

**Disposition: Accept — fully implemented**

Original memo used classic memo structure (Executive Summary / Background / Method / Findings / Implications / Limitations). Revised to the six spec-required headings:

| Before | After |
|--------|-------|
| Executive Summary | § 2. Selection Rationale |
| Background | § 1. Company Overview |
| Method / References | § 3. Data Availability & Sources |
| Findings (hypothesis questions) | § 4. Preliminary Observations |
| *(absent)* | § 5. Ratio Categories Preview |
| Implications / Limitations / Stage 3 Priority | § 6. Data Collection Plan |

All six sections now present in spec order in the revised memo.

---

### Suggestion 2 — Flip five hypothesis questions into "I expect X because Y" directional claims

**Disposition: Accept — fully implemented**

Original: five open-ended questions ("What drives the gross-to-net gap?").
Revised: five committed directional statements in Section 4, each falsifiable by Stage 3 ratios. Examples:

- "I expect FY2026 gross margin to compress a further 100–200 bps because total cost of revenues fell only 1.9% while revenue fell 6.6%, indicating cost structure does not scale down with revenue."
- "I expect the constant-currency revenue decline to be worse than the reported 6.6% because RMB appreciated 4.19% against USD in FY2025 — a headwind for ADS investors."
- "I expect beta (0.19) to understate true equity risk because it cannot capture PRC-specific tail risks — content bans, VIE unwinding — which is why I add a 3% China risk premium to WACC."

Each hypothesis takes a position the Stage 3 ratio results can confirm or falsify.

---

### Suggestion 3 — Tighten word count from ~1,056 to ~500

**Disposition: Accept — implemented; final count 530 words**

"Findings" and "Implications" sections were merged and restructured. Forward-looking ratio plumbing moved from "Limitations & Next Steps" into Section 6 (Data Collection Plan). Redundant narrative removed. Final word count: **530 words** (within the 400–600 target).

---

### Suggestion 4 — Filename and frontmatter housekeeping

**Disposition: Accept — fully implemented**

| Item | Before | After |
|------|--------|-------|
| Filename | `2026-05-14-phuong-iqiyi-selection-memo.md` | `2026-05-14-luong-iqiyi-selection.md` |
| YAML `template` | absent | `memo` |
| YAML `stage` | absent | `2` (via `course: BUS-629`) |
| YAML `author` | absent | `Luong Duy Phuong` (in header) |
| YAML `company` / `ticker` | absent | `iQIYI Inc`, `IQ`, `NASDAQ` (in header and §1) |

Filename now matches the canonical `YYYY-MM-DD-{lastname}-{company-slug}-selection.md` pattern required by Stage 4/5 tooling.

---

### Suggestion 5 — Add `adamwstauffer` as Write collaborator on the repo

**Disposition: Accept — completed**

`adamwstauffer` added as Write collaborator via GitHub Settings → Collaborators. PR-style feedback flow enabled for Stage 4 and Stage 5 reviews.

---

### Suggestion 6 — Reconcile Mergent's EBITDA figure (50.4%) against the 20-F CF statement in Stage 3

**Disposition: Accept — actioned in Stage 3**

Stage 3 workbook (`models/builds/2026-05-14-luong-iqiyi-financials.xlsx`) computes EBITDA from the Cash Flow Statement: Net Income + Tax + Interest + D&A add-back (`CASH_depreciation_amortization` = RMB 13,264,120K). This approach is consistent with iQIYI embedding all D&A in cost of revenues — the `INC_depreciation` IS line is zero, so the CF Statement is the only valid D&A source. The resulting EBITDA margin reconciles with the Mergent figure within rounding tolerance. This finding became a key analytical point in Stage 4 (Cash Coverage = 14.83x vs. TIE = 0.25x) and Stage 5 (Baidu D&A injection narrative in Section 6 Executive Justification).

---

## Net Assessment

All six suggestions were structural or procedural — none required revising the analytical substance, which the instructor described as "one of the strongest analytical memos in the cohort." The revision converted the memo from a general-purpose format into the spec-compliant Stage 2 structure while preserving the original hypothesis framing and data trail.
