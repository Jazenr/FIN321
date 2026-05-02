# Stage 4 – FX Hedge Final Analysis & Recommendation
## Scenario 4 – U.S. Aerospace Manufacturer | EUR/USD Receivable

**Author:** Jazen Rodrigues
**Date:** May 1, 2026
**Version:** 2.0 (Updated with live market data)
**LLM Assisted:** Claude Sonnet 4.6

---

## A. Exposure Summary

### The Receivable

A U.S. aerospace manufacturer expects to receive **€20,000,000** from a European customer in approximately one year (maturity: May 1, 2027). Because the firm reports in U.S. dollars, all euro cash inflows must eventually be converted into USD. This creates **transaction exposure**: the dollar value of the receivable fluctuates with the EUR/USD exchange rate between now and collection.

### FX Risk

The primary risk is **EUR depreciation**. If the euro weakens relative to the dollar over the next twelve months, each euro received will convert to fewer dollars, compressing the firm's realized revenue and cash flow below its budgeted level. Conversely, EUR appreciation would increase dollar proceeds — but that upside must be weighed against the cost and constraints of any hedging instrument chosen.

### Market Context (as of May 1, 2026)

| Variable | Value | Source |
|---|---|---|
| Spot Rate (S₀) | **1.1734 USD/EUR** | Federal Reserve H.10 / FRED |
| 1-Year Forward Rate (F₀) | **1.1921 USD/EUR** | CIP-derived (see below) |
| USD Interest Rate (R_USD) | **3.625%** | FOMC target midpoint, April 29, 2026 |
| EUR Interest Rate (R_FC) | **2.00%** | ECB Deposit Facility Rate, April 30, 2026 |
| Put Strike (K_PUT) | **1.1734** | At-the-money (spot) |
| Call Strike (K_CALL) | **1.1734** | At-the-money (spot) |
| Put Premium (PREM_PUT) | **$0.019/EUR** ($380,000 total) | Scenario given |
| Call Premium (PREM_CALL) | **$0.024/EUR** ($480,000 total) | Scenario given |
| Horizon (T_DAYS) | **365 days** | Contract maturity |

**Forward Rate Derivation (Covered Interest Rate Parity):**

```
F₀ = S₀ × (1 + R_USD) / (1 + R_FC)
F₀ = 1.1734 × (1.03625) / (1.02000)
F₀ = 1.1734 × 1.015931
F₀ ≈ 1.1921 USD/EUR
```

> **Note on Forward Rate:** The original Stage 1 memo cited a scenario forward of 1.0935. With the updated spot of 1.1734 and current interest rates, the CIP-implied forward is 1.1921. This revised rate is used throughout Stage 4 for consistency with live market inputs. The dollar premium on USD (positive carry) reflects the 162.5 bps rate differential, pushing the forward above spot.

### Business Context

The receivable represents a material cash inflow. At today's spot, unhedged dollar proceeds would total approximately **$23,468,000**. A ±5% move in EUR/USD translates to a swing of roughly **±$1,174,000** — a magnitude that can materially affect quarterly earnings, budget variance, and covenants. Given this level of exposure, hedging is prudent and warrants a structured analysis.

---

## B. Summary of Hedge Outcomes

All proceeds are calculated on FC_AMT = €20,000,000, assuming the firm elects to hedge the full notional.

### Forward Hedge

**Locked-in USD Proceeds:** $23,842,000

```
Proceeds = FC_AMT × F₀ = 20,000,000 × 1.1921 = $23,842,000
```

The forward hedge eliminates all FX uncertainty. The firm locks in $23.84M regardless of where EUR/USD settles at maturity. This exceeds the current unhedged value at spot by approximately $374,000 — a benefit of the positive USD carry. The tradeoff is complete elimination of upside: if EUR strengthens to 1.25, the firm still receives $23.84M.

### Money Market Hedge (3-Step Synthetic Forward)

**Locked-in USD Proceeds:** ~$23,842,000

**Step 1 – Borrow EUR today (PV of receivable):**
```
PV_EUR = 20,000,000 / (1 + 0.02) = €19,607,843.14
```

**Step 2 – Convert to USD at spot:**
```
USD_today = 19,607,843.14 × 1.1734 = $23,007,843
```

**Step 3 – Invest USD at R_USD for 1 year:**
```
FV_USD = 23,007,843 × (1 + 0.03625) = $23,841,877
```

The money market hedge produces proceeds virtually identical to the forward (~$23,842,000), confirming **covered interest rate parity**. This is the expected result. The strategy is more operationally complex — requiring a EUR-denominated credit facility and active cash management — and introduces liquidity considerations: the firm must manage the dollar investment and EUR borrowing simultaneously.

### Put Option Hedge

**Floor (worst case):** $23,088,000 | **Upside:** Unlimited (net of premium)

```
Total Premium = 20,000,000 × $0.019 = $380,000
Floor = (20,000,000 × 1.1734) − 380,000 = $23,468,000 − $380,000 = $23,088,000
```

If EUR/USD falls below 1.1734 at maturity, the firm exercises the put and receives $23,468,000 from converting at the strike, then subtracts the $380,000 premium for a net floor of **$23,088,000**. If EUR/USD rises above 1.1734, the put expires worthless and the firm converts at the prevailing market rate — capturing upside — minus the $380,000 premium. This strategy offers the best of both worlds at the cost of a defined, upfront premium.

### Call Option

**Not a standard receivable hedge.** A call on EUR grants the right to *buy* EUR at the strike — the opposite of what a EUR receivable holder needs. Holding the receivable plus a long EUR call amplifies EUR exposure rather than hedging it:

```
Net (S_T ≤ K) = 20M × S_T − $480,000   [call expires, net proceeds lower by premium]
Net (S_T > K) = 20M × S_T + 20M × (S_T − 1.1734) − $480,000   [leveraged upside]
```

A long EUR call is a speculative overlay, not a hedge for a EUR receivable. It provides no downside floor and costs $480,000 in premium. It is presented here for analytical completeness. The appropriate option hedge for this exposure is the **put**.

### No Hedge (Unhedged Baseline)

```
USD Proceeds = 20,000,000 × S_T
```

At today's spot (1.1734): **$23,468,000** — $374,000 less than the forward rate, illustrating the cost of inaction relative to locking in the positive carry. Full upside and downside exposure remain.

---

## C. Sensitivity Interpretation

The table below shows net USD proceeds across a ±5% range of EUR/USD outcomes at maturity (T = 365 days):

| S_T Scenario | Δ vs Spot | No Hedge | Forward | Money Mkt | Put Option | Call Option |
|---|---|---|---|---|---|---|
| **1.1147** | −5% | $22,294,000 | $23,842,000 | $23,842,000 | $23,088,000 | $21,814,000 |
| **1.1440** | −2.5% | $22,880,000 | $23,842,000 | $23,842,000 | $23,088,000 | $22,400,000 |
| **1.1734** | 0% (Spot) | $23,468,000 | $23,842,000 | $23,842,000 | $23,088,000 | $22,988,000 |
| **1.2027** | +2.5% | $24,054,000 | $23,842,000 | $23,842,000 | $23,674,000 | $24,628,000 |
| **1.2321** | +5% | $24,642,000 | $23,842,000 | $23,842,000 | $24,262,000 | $25,682,000 |

> Put Option proceeds when S_T ≤ K_PUT = (20M × 1.1734) − $380,000 = $23,088,000 (floor).
> Put Option proceeds when S_T > K_PUT = (20M × S_T) − $380,000.
> Call Option proceeds = (20M × S_T) + max(0, (S_T − 1.1734) × 20M) − $480,000.

### EUR Depreciation Scenarios (S_T < 1.1734)

Forward and money market hedges are the clear winners — they eliminate downside entirely, locking in $23,842,000 regardless of EUR weakness. The put option provides a floor of $23,088,000, which is $754,000 below the forward, reflecting the option premium. No hedge and the call option both result in meaningful cash flow deterioration, with the call performing worst due to the $480,000 premium drag.

### EUR Appreciation Scenarios (S_T > 1.1734)

The unhedged and call option positions benefit most from EUR strength. The put option participates in EUR appreciation (net of the $380,000 premium) — at S_T = 1.2321, it yields $24,262,000 vs. $24,642,000 unhedged — an $380,000 "cost of insurance." The forward and money market strategies cap out at $23,842,000, forgoing substantial upside if EUR rallies.

### Key Insight

The current macro environment warrants close attention. The ECB held rates at 2.00% in April 2026, but markets are pricing roughly 75 bps of hikes by year-end — driven by Middle East-related energy inflation pushing Eurozone CPI to 3.0%. EUR appreciation risk is non-trivial. A strategy preserving upside (put option) is therefore materially valuable in the current environment, not merely an academic alternative to the forward.

---

## D. Strategic Recommendation

**Recommended Strategy: EUR Put Option (with at-the-money strike K = 1.1734)**

The put option is the optimal strategy for this firm given current market conditions. It provides:

1. **Absolute downside protection** — the $23,088,000 floor ensures the firm never converts below $1.1734/EUR regardless of EUR weakness.
2. **Full participation in EUR appreciation** — given hawkish ECB signals and energy-driven inflation, EUR could strengthen materially over the next 12 months. The forward hedge forfeits this upside.
3. **Defined, manageable cost** — $380,000 total premium on a $23M+ receivable equals a hedge cost of approximately **1.62%** of notional — reasonable insurance for a U.S. aerospace firm with multi-year international contracts and tight margins.

**Secondary consideration:** A collar structure (buying the put at 1.1734 and selling a call at, say, 1.22–1.25) could reduce net premium cost if the CFO is willing to cap EUR upside at a higher level. This is not the primary recommendation, but it optimizes cost efficiency.

---

## E. Executive Justification

**Cash Flow Stability:** The put option establishes a minimum USD cash flow of **$23,088,000**, which enables reliable budget planning, covenant compliance, and working capital management for the next fiscal year. The forward hedge would offer $754,000 more certainty but at the full cost of any EUR upside.

**Budget Certainty vs. Optionality:** The current ECB posture — holding at 2.00% but signaling potential hikes — creates a genuine two-sided EUR/USD outlook. Unlike a forward, which locks in a single outcome, the put preserves economic flexibility. The $380,000 premium is best viewed not as a cost but as the price of retaining optionality valued at potentially $1M+ if EUR rallies to 1.25+.

**Liquidity Impact:** Unlike the money market hedge, the put option requires no credit line, no EUR borrowing facility, and no active cash investment. It requires only the upfront premium — a single clean cash outflow. This simplicity is operationally attractive for a corporate treasury team managing multiple exposures.

**Premium Costs:** At $0.019/EUR, the put premium is modest — 1.6 cents for every dollar of EUR/USD at spot. This is well within typical at-the-money EUR option pricing. The forward, while "free" to enter, effectively charges an implicit opportunity cost: surrendering any USD gain from EUR appreciation. On a €20M receivable, EUR appreciation from 1.1734 to 1.20 would represent approximately $534,000 in foregone proceeds under the forward.

**Accounting Implications (Optional):** Under ASC 815, a designated cash flow hedge using a purchased put would allow effective hedge gains/losses to flow through Other Comprehensive Income (OCI) until the hedged item (receivable conversion) affects earnings. The premium cost is amortized over the hedge period. This accounting treatment reduces P&L volatility relative to an undesignated position.

---

## F. Structured AI Prompt

*Appendix: Reproducible AI Prompt for Excel Model Generation*

---

```
# GOAL

Create a complete, fully functional Microsoft Excel workbook (.xlsx) that models
four foreign exchange hedging strategies for a EUR receivable. The model must be
audit-ready, reproducible, and match the specifications below exactly.

# INPUT VARIABLES

Use the following named ranges and values. All inputs go in a dedicated "Inputs"
section color-coded YELLOW (fill: hex #FFFF00):

  FC_AMT       = 20000000         (EUR receivable amount, no multiplier)
  S0_in        = 1.1734           (EUR/USD spot rate, May 1, 2026)
  F0_in        = 1.1921           (1-year forward rate, CIP-derived)
  R_USD        = 0.03625          (USD interest rate, FOMC midpoint 3.625%)
  R_FC         = 0.02000          (EUR interest rate, ECB deposit facility 2.00%)
  K_PUT        = 1.1734           (Put strike, at-the-money)
  K_CALL       = 1.1734           (Call strike, at-the-money)
  PREM_PUT     = 0.019            (Put premium per EUR, in USD)
  PREM_CALL    = 0.024            (Call premium per EUR, in USD)
  T_DAYS       = 365              (Hedge horizon in days)

# SPREADSHEET ARCHITECTURE

## Color Coding (mandatory, no exceptions)
  - YELLOW  (#FFFF00): Input cells — all values in the Input section
  - BLUE    (#ADD8E6): Assumption cells — derived constants, rate labels
  - GREEN   (#90EE90): Formula cells — all calculated outputs
  - GRAY    (#D3D3D3): Output summary cells — final proceeds by strategy

## Sheet Structure (use one sheet named "FX_Hedge_Model")
  Row 1-2:    Title block: "FX Hedge Analysis – EUR Receivable | Scenario 4"
  Row 3-14:   INPUT SECTION (named ranges as above, yellow fill)
  Row 16-22:  FORWARD HEDGE section
  Row 24-32:  MONEY MARKET HEDGE section (3-step)
  Row 34-44:  OPTION HEDGES section (put and call)
  Row 46-60:  SENSITIVITY TABLE
  Row 62-68:  VERIFICATION CHECKS
  Row 70-78:  OUTPUT SUMMARY (gray fill)

# MODEL LOGIC

## Section 1: Forward Hedge
  Label: "Forward Hedge"
  Formula: =FC_AMT * F0_in
  Output label: "Locked-in USD Proceeds"
  Expected result: $23,842,000

## Section 2: Money Market Hedge (3 Steps)
  Step 1 – Borrow PV of EUR receivable today:
    PV_EUR = =FC_AMT / (1 + R_FC)
    Expected: €19,607,843.14
  Step 2 – Convert PV_EUR to USD at spot:
    USD_today = =PV_EUR * S0_in
    Expected: $23,007,843
  Step 3 – Invest USD_today at R_USD for T_DAYS/365:
    FV_USD = =USD_today * (1 + R_USD * T_DAYS/365)
    Expected: ~$23,841,877
  Label result: "Money Market Locked-in USD Proceeds"

## Section 3: Option Hedges
  ### Put Option
    Total_Premium_Put = =FC_AMT * PREM_PUT         → $380,000
    Floor_Put         = =(FC_AMT * K_PUT) - Total_Premium_Put  → $23,088,000
    Label: "Put Option Floor (worst-case USD proceeds)"
    Note: "Upside unlimited above K_PUT, net of premium"

  ### Call Option
    Total_Premium_Call = =FC_AMT * PREM_CALL       → $480,000
    Label: "Call Option – Speculative overlay (not primary hedge)"
    Note: "Long EUR call on a EUR receivable amplifies exposure; presented for comparison only"

## Section 4: No Hedge Baseline
    At_Spot = =FC_AMT * S0_in                      → $23,468,000
    Label: "Unhedged Value at Today's Spot"

# SENSITIVITY TABLE

Build a two-dimensional sensitivity table with:
  Columns: S_T scenarios (5 values, ±5% range around S0_in)
    S_T_1 = =S0_in * 0.95     → 1.1147
    S_T_2 = =S0_in * 0.975    → 1.1441
    S_T_3 = =S0_in            → 1.1734
    S_T_4 = =S0_in * 1.025    → 1.2027
    S_T_5 = =S0_in * 1.05     → 1.2321

  Rows: One row per strategy
    No Hedge:      =FC_AMT * S_T
    Forward:       =FC_AMT * F0_in   (constant across all S_T)
    Money Market:  =FV_USD           (constant; reference Section 2 result)
    Put Option:    =IF(S_T < K_PUT, (FC_AMT * K_PUT) - Total_Premium_Put,
                       (FC_AMT * S_T) - Total_Premium_Put)
    Call Option:   =(FC_AMT * S_T) + MAX(0, (S_T - K_CALL) * FC_AMT) - Total_Premium_Call

  Format all sensitivity cells as currency (USD, 0 decimal places), GREEN fill.
  Add conditional formatting: highest value in each column = DARK GREEN, lowest = RED.

# VERIFICATION CHECKS

Add the following checks in Row 62-68 (label section "Parity Checks"):

  Check 1 – CIP Verification:
    CIP_Forward = =S0_in * (1 + R_USD) / (1 + R_FC)
    Check: =IF(ABS(CIP_Forward - F0_in) < 0.001, "PASS – CIP holds", "FAIL – Review rates")

  Check 2 – Forward vs. Money Market Parity:
    Diff = =ABS(Forward_Proceeds - FV_USD)
    Check: =IF(Diff < 100, "PASS – Strategies equivalent", "FAIL – Review formulas")

  Check 3 – Put Floor Verification:
    Put_Floor_Check = =(FC_AMT * K_PUT) - Total_Premium_Put
    Check: =IF(ABS(Put_Floor_Check - Floor_Put) < 1, "PASS", "FAIL")

  Fill all PASS cells GREEN, all FAIL cells RED.

# FORMATTING REQUIREMENTS

- Font: Calibri 11pt throughout
- Title: Calibri 14pt Bold
- Section headers: Calibri 12pt Bold, dark blue font
- Number format: USD currency for dollar amounts, 4 decimal places for rates
- Freeze row 1 and column A
- Set print area to cover all sections
- Add a footer: "Scenario 4 | Jazen Rodrigues | May 1, 2026 | Confidential"
- Name the workbook tab: "FX_Hedge_Model"

# EXPORT

Save as: stage2-fx-hedge-model-Rodrigues.xlsx
Confirm all named ranges are defined in the Name Manager before saving.

# SCENARIO METADATA

Include a small reference block (blue fill) in rows 3-4:
  "Market data as of: May 1, 2026"
  "Spot source: Federal Reserve H.10 (FRED)"
  "USD Rate source: FOMC Statement, April 29, 2026"
  "EUR Rate source: ECB Monetary Policy Decision, April 30, 2026"
```

---

## Extra Credit: Areas for Further Study & Improvement

### 1. AI Skills & Automation

The structured prompt in Section F above is already designed for AI-assisted regeneration, but it could be made far more powerful with tool integration. Claude Skills or a Code Interpreter environment could pull live EUR/USD spot data from the Federal Reserve's FRED API, current FOMC and ECB rates from their official press release endpoints, and at-the-money option implied volatilities from a derivatives data provider — then automatically substitute those values into the named range block before running the model. This eliminates the manual "update variables" step that was required for this Stage 4 submission. More ambitiously, a Monte Carlo simulation layer could be added: instead of a static ±5% sensitivity table, the AI could generate 10,000 EUR/USD paths using a geometric Brownian motion model calibrated to the current implied volatility surface, producing a full probability distribution of hedge outcomes. This would transform a point-estimate model into a risk quantification engine used in production treasury systems.

### 2. Multi-File Reasoning & GitHub Version Control

Stages 1 through 4 of this project represent a natural multi-file reasoning task: Stage 1 defines the exposure, Stage 2 specifies variables, Stage 3 defines the model architecture, and Stage 4 delivers the final recommendation. An AI with access to all four files simultaneously could verify cross-stage consistency — flagging, for example, that the original Stage 1 forward rate (1.0935) was inconsistent with Stage 4's updated CIP-implied forward (1.1921) and prompting the analyst to resolve the discrepancy. GitHub version control makes this workflow auditable and reproducible. By committing each stage's `.md` and `.xlsx` artifacts to a repository with descriptive commit messages, any reviewer — internal audit, an investment bank's risk team, or a graduate program admissions officer — can reconstruct exactly how the model evolved, what assumptions changed between versions, and why. This directly mirrors the model governance workflows at Big 4 firms and corporate treasury departments, where version-controlled model artifacts serve as audit evidence for hedge accounting documentation under ASC 815 and IFRS 9.

---

*End of Stage 4 Deliverable*
*Word count: ~2,200 | Pages: ~3.5 | Format: Markdown (.md)*
