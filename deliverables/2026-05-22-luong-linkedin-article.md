---
purpose: "LinkedIn article — cross-vendor LLM testing as a spec validation methodology"
author: Luong Duy Phuong
date: 2026-05-22
platform: LinkedIn
word_count_target: 1500
published_url: "https://www.linkedin.com/pulse/cross-vendor-llm-testing-spec-validation-methodology-author-phuong-uaavc/"
---

# Why I Tested My Financial Analysis Spec on Two LLMs — and What the Differences Revealed

I recently completed a structured financial analysis of iQIYI Inc (IQ, NASDAQ) — China's largest streaming platform — as part of my MBA coursework at the University of Hawaiʻi at Mānoa, Shidler College of Business.

The assignment wasn't just "analyze the company." It required building a formal written spec — essentially a structured instruction document — that could be handed to any LLM and reliably produce a correct, complete 30-ratio financial analysis from SEC Form 20-F data.

What started as a course deliverable became something I hadn't expected: a small methodology study on where LLMs diverge, and what those divergences reveal about the specs we write.

---

## What Is a Spec-Driven Financial Analysis?

The workflow works like this:

1. You build a financial model in Excel with named ranges (`BAL_assets_total`, `INC_net_income`, `CASH_depreciation_amortization`, etc.)
2. You write a spec — a structured document that instructs an LLM on which named ranges to use, how to compute each ratio, what to analyze, and how to format the output
3. You hand that spec to an LLM with no additional context and evaluate whether the output is correct

The spec is doing the work that a senior analyst's judgment normally does: deciding which D&A line to use, how to handle multi-currency inputs, which year-end equity figure belongs in which formula. If the spec is ambiguous, the LLM fills the gap with its own inference — and that inference may or may not be right.

Human-in-the-loop (HIL) iteration means you test the spec, find the gaps, fix them, and test again. I ran 5 rounds. Along the way, I added a second LLM — Gemini — to test whether the spec was robust across vendors, not just optimized for one model's behavior.

That decision changed everything.

---

## Three Gaps That Tell the Story

### Gap 1: The Error That Looks Correct

iQIYI is a Chinese company listed as an ADR on NASDAQ. Its financial statements are in RMB. Its stock price is in USD.

My spec v1 included a Market-to-Book formula: `market_capitalization / currentYear_equity`. Clean and standard.

What the spec didn't specify: `market_capitalization` was in USD thousands. `currentYear_equity` was in RMB thousands. Dividing the two produces a dimensionless ratio with no financial meaning — but the number came out to **0.085x**, which looks plausible. Market-to-book below 1 is common in distressed companies. Nothing flagged.

It took a separate cross-check — asking "what currency are the inputs in?" rather than "is the output reasonable?" — to catch it.

The fix: add a `market_capitalization_rmb` derived input (`market_capitalization × fx_rate = 1,136,292 × 6.9931 = 7,946,204 RMB '000`) and update every formula to use the converted figure.

Correct result: **0.597x**. The difference between "the market is pricing this at nearly zero" and "the market is pricing this at a 40% discount to book value" — two very different investment signals.

**The lesson:** Multi-currency specs need a mandatory unit conversion block *before* any ratio formula. An output that looks plausible is not the same as an output that is correct.

---

### Gap 2: The Zero That Shouldn't Have Been There

Cash Coverage ratio measures whether a company can service its debt using operating cash flow. The formula adds D&A back to EBIT in the numerator: `(EBIT + D&A) / Interest Expense`.

iQIYI's income statement shows `INC_depreciation = 0`.

This is not because iQIYI has no depreciation — it's because iQIYI embeds all D&A inside cost of revenues rather than reporting it as a separate line item. The actual D&A figure (`CASH_depreciation_amortization = RMB 13,264,120K`) sits in the Cash Flow Statement's operating section.

My spec v1 didn't mention this. Any LLM executing the formula would look for D&A on the income statement, find zero, and either collapse Cash Coverage to the same value as TIE — or, worse, attempt a decomposition of COGS that the model doesn't support.

TIE for iQIYI is **0.25x** — well below safe thresholds, signaling real solvency risk.

Cash Coverage, correctly computed, is **14.83x** — an entirely different picture, explained by RMB 13.3B of non-cash D&A that Baidu (iQIYI's controlling shareholder) is effectively injecting into the business.

Both ratios are correct. But they tell opposite stories, and which one you trust depends on whether you know where the D&A is hiding.

**The lesson:** Company-specific accounting structures must be documented in the spec. A named range that exists in the model but differs from the template default is a gap waiting to become an error.

---

### Gap 3: The Contradiction Neither Model Noticed

After Round 2, I had fixed a subtle error in my spec's validation table (§7): an explanation of why Du Pont ROE and direct ROE produce slightly different results for iQIYI (3 basis points — a methodological artifact from using different year-end equity figures, not a computation error).

What I didn't do: update the executor instructions section (§9), which described the same concept in different words — the original, wrong words.

For four test rounds, this went undetected. Claude consistently deferred to §7 (the validation table) when the two sections conflicted.

Then I tested with Gemini.

Gemini deferred to §9 (the executor instructions). It reproduced the original wrong explanation — the one I thought I had fixed three rounds earlier.

Both models were making a locally reasonable choice about which section to trust when two sections disagreed. But they were making *different* choices, and only the cross-vendor test made that visible.

**The lesson:** A spec that passes testing on one LLM is not a validated spec — it's a spec validated for that model's resolution heuristics. Cross-vendor testing is, in practice, a contradiction-detection mechanism. The discrepancy between models is the signal, not the noise.

---

## What This Means for Anyone Building LLM-Assisted Analytical Workflows

These three gaps share a structure:

1. **The FX gap** — the spec didn't declare a required transformation step. The LLM applied the formula as written and produced a plausible-but-wrong number.
2. **The D&A gap** — the spec didn't document a company-specific deviation from standard accounting. The LLM would have defaulted to the template assumption, which was zero.
3. **The §7/§9 gap** — the spec had internal inconsistency across sections. Different models resolved it differently, making the inconsistency invisible to single-model testing.

All three are spec problems, not LLM problems. The models did exactly what the instructions told them to do. The discipline of spec-driven analysis is precisely this: when the output is wrong, you look at the spec first.

The cross-vendor testing protocol operationalizes that discipline. If your spec is genuinely unambiguous, both Claude and Gemini should produce the same output. When they don't, you have a specific location to inspect — not "something is wrong" but "these two sections say different things, and here's which model deferred to which."

After 5 rounds of iteration and 10 documented gaps closed, spec v2.0 produced a clean execution: 30 ratios, all values verified manually, no hallucinated figures across any input.

The final output is a complete ratio analysis of iQIYI — a company with a TIE of 0.25x, a Market-to-Book of 0.597x, and an accumulated deficit of RMB 42.7B that overwhelms RMB 56.0B of paid-in capital to produce a positive (but fragile) book equity. The conclusion: do not invest. But the methodology story is the one worth telling.

---

## One Takeaway

If you're building workflows where LLMs perform structured analysis on financial, legal, or operational data:

**Test your spec on two different model families before you trust the output.**

Not to find which model is better. To find where your spec is ambiguous — because the models will tell you, just not directly. They'll tell you by disagreeing.

The full iteration log, spec, and analysis are publicly available on GitHub: [github.com/duyphuonglt-gif/Corporate-Finance](https://github.com/duyphuonglt-gif/Corporate-Finance)

---

*Luong Duy Phuong | MBA Candidate, Shidler College of Business, University of Hawaiʻi at Mānoa | BUS-629 International Corporate Finance*
