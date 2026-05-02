# FX Receivable Hedge Model – Technical Specification (Stage 3)

**Created by:** Jazen Rodrigues
**Updated by:** Jazen Rodrigues
**Date Created:** 2026-04-03
**Date Updated:** 2026-04-24
**Version:** 2.0
**LLM Used:** Claude Sonnet 4.6

**Role:** Financial Analyst / Treasury Analyst
**Audience:** CFO, Director of Treasury

**Purpose:** Provide a professional, quantitative specification documenting the Stage 2 hedge model, correcting identified errors, and articulating a rigorous improved design that a treasury analyst or AI model-builder can implement without further clarification.

---

## 1. Problem Statement

A U.S. aerospace manufacturer expects to receive **€20,000,000 in 12 months** under a contractual euro-denominated sales agreement. Because the firm reports financial results in USD, this receivable is fully exposed to EUR/USD exchange-rate risk: if the euro depreciates over the coming year, the dollar equivalent of the collection will fall below today's projected value, reducing earnings and creating cash flow unpredictability. Even a 5% EUR depreciation from current spot would cost approximately **$1.2 million** in realized USD proceeds on this single receivable.

The objective of this model is to quantify USD proceeds under four scenarios — no hedge, forward hedge, money market hedge, and EUR put option hedge — across a range of possible future EUR/USD rates, and to provide the CFO with a reproducible, auditable basis for selecting the optimal hedging strategy prior to the receivable's settlement date.

---

## 2. Inputs (Known Variables)

All inputs are recorded as of the model's market-close reference date of **April 17, 2026**.

| Named Range   | Description                                 | Value         | Unit         | Source                              |
|---------------|---------------------------------------------|---------------|--------------|-------------------------------------|
| `FC_AMT`      | Euro-denominated receivable                 | 20,000,000    | EUR          | Company contract data               |
| `S0_IN`       | EUR/USD spot rate at model inception        | 1.1797        | USD per EUR  | Market close, Apr 17 2026           |
| `F0_IN`       | EUR/USD 1-year forward rate (scenario-given)| 1.0935        | USD per EUR  | Scenario 4 assignment specification |
| `R_USD`       | USD 1-year interest rate (domestic)         | 3.69%         | Annual, simple | Market proxy, Apr 17 2026         |
| `R_FC`        | EUR 1-year interest rate (foreign)          | 2.00%         | Annual, simple | Market proxy, Apr 17 2026         |
| `T_DAYS`      | Days to settlement                          | 360           | Days         | Model convention (30/360 basis)     |
| `K_PUT`       | EUR put option strike                       | 1.1797        | USD per EUR  | Set at-the-money (equal to `S0_IN`) |
| `K_CALL`      | EUR call option strike                      | 1.1797        | USD per EUR  | Set at-the-money (equal to `S0_IN`) |
| `PREM_PUT`    | EUR put premium per unit of EUR             | 0.019         | USD per EUR  | Scenario 4 assignment specification |
| `PREM_CALL`   | EUR call premium per unit of EUR            | 0.024         | USD per EUR  | Scenario 4 assignment specification |

> **Note on Forward Rate:** The scenario specification provides `F0_IN = 1.0935` as a given market input. The Stage 2 model incorrectly overrode this with an IRP-derived forward of approximately 1.1993 (computed as `S0_IN × (1 + R_USD) / (1 + R_FC)`). While IRP-based forward verification is a valid cross-check, the assigned scenario forward of **1.0935 must be the primary locked-in rate** used for all forward hedge calculations, as it represents the actual rate the firm would contract with a bank counterparty.

---

## 3. Assumptions & Constraints

- Exchange rates are expressed as **USD per EUR** throughout (direct quote from a U.S. firm's perspective).
- Interest rates are quoted on a **simple annual basis** with a **360-day year** (`T_DAYS / 360`).
- The forward rate `F0_IN = 1.0935` is treated as a contractually available market rate for the full notional `FC_AMT`, not recalculated from IRP.
- Option premiums (`PREM_PUT`, `PREM_CALL`) are quoted in **USD per EUR** with **no contract size multiplier** (i.e., each unit of EUR costs the stated premium directly).
- Option premiums are paid **upfront in USD** at inception and their **future value at maturity** is deducted from gross proceeds in all option scenarios. The future-value factor applied is `(1 + R_USD × T_DAYS/360)`.
- The EUR put option grants the right to **sell EUR at K_PUT**. For a EUR receivable, exercising the put is rational when `S_T < K_PUT`; the put expires worthless when `S_T ≥ K_PUT`.
- The EUR call option grants the right to **buy EUR at K_CALL**. For a EUR receivable holder, this instrument does **not** provide downside protection against EUR depreciation; it is modeled here for completeness and educational comparison only. It would be exercised when `S_T > K_CALL`.
- Transaction costs, bid-ask spreads, margin requirements, credit risk, and tax effects are excluded.
- The money market hedge is constructed under the **interest rate parity (IRP)** framework and should produce proceeds equal to the forward hedge within rounding tolerance, confirming internal consistency.
- All sensitivity scenarios use **end-of-period spot rates (`S_T`)** ranging from `0.90 × S0_IN` to `1.10 × S0_IN` in increments of `0.01`, producing 21 scenario rows.

---

## 4. Calculation Flow

### 4.1 Forward Hedge

1. The firm contracts today to deliver `FC_AMT` EUR in one year at `F0_IN`.
2. **USD proceeds** = `FC_AMT × F0_IN` = €20,000,000 × 1.0935 = **$21,870,000**.
3. This amount is locked regardless of `S_T`. Record as `USD_FWD`.

### 4.2 Money Market Hedge

1. **Borrow in EUR:** Borrow the present value of the receivable today so that, after one year of interest, the loan is repaid exactly by the €20M collection.
   - Borrow amount = `FC_AMT / (1 + R_FC × T_DAYS/360)` = 20,000,000 / 1.02 = **€19,607,843.14**
2. **Convert to USD at spot:** Convert the borrowed EUR to USD immediately.
   - USD today = `Borrow_amount × S0_IN` = 19,607,843.14 × 1.1797 = **≈ $23,131,373**
3. **Invest USD:** Invest the USD proceeds at the domestic rate for one year.
   - `USD_MM` = `USD_today × (1 + R_USD × T_DAYS/360)` = 23,131,373 × 1.0369 = **≈ $23,984,920**
4. Cross-check: `USD_MM` should closely approximate `FC_AMT × IRP_Forward`, where `IRP_Forward = S0_IN × (1 + R_USD)/(1 + R_FC)`. Any difference from `USD_FWD` (based on the given `F0_IN = 1.0935`) is expected and should be flagged in the output as a **forward vs. IRP basis** line item — it does not indicate a model error.

### 4.3 EUR Put Option Hedge

1. **Premium outflow (upfront):** `Total_Prem_Put = PREM_PUT × FC_AMT` = 0.019 × 20,000,000 = **$380,000**
2. **Future value of premium:** `FV_Prem_Put = Total_Prem_Put × (1 + R_USD × T_DAYS/360)` = 380,000 × 1.0369 = **$394,022**
3. **Net USD proceeds at each `S_T`:**
   - If `S_T < K_PUT` (put exercised): `USD_PUT(S_T) = K_PUT × FC_AMT − FV_Prem_Put`
     = 1.1797 × 20,000,000 − 394,022 = **$23,199,978** (floor)
   - If `S_T ≥ K_PUT` (put expires): `USD_PUT(S_T) = S_T × FC_AMT − FV_Prem_Put`
     = (S_T × 20,000,000) − 394,022
4. The minimum guaranteed net proceed is **$23,199,978** — this is the effective floor for the put hedge.

### 4.4 EUR Call Option (Educational / Comparison Only)

1. **Premium outflow (upfront):** `Total_Prem_Call = PREM_CALL × FC_AMT` = 0.024 × 20,000,000 = **$480,000**
2. **Future value of premium:** `FV_Prem_Call = Total_Prem_Call × (1 + R_USD × T_DAYS/360)` = 480,000 × 1.0369 = **$497,712**
3. **Net USD proceeds at each `S_T`:**
   - The firm already holds EUR (the receivable). Buying a EUR call confers the right to buy more EUR — this does not hedge. Net proceeds = `S_T × FC_AMT − FV_Prem_Call` for all `S_T` (call is never rationally exercised as a hedge overlay).
   - Include for illustration only; do **not** recommend as a standalone hedge.

### 4.5 No-Hedge Baseline

- `USD_UNHEDGED(S_T) = S_T × FC_AMT`
- This is the benchmark against which all hedge outcomes are compared.

### 4.6 Sensitivity Table

- For each `S_T` in the range `[0.90 × S0_IN, 1.10 × S0_IN]` at $0.01 steps (21 rows), calculate:
  - `USD_UNHEDGED`, `USD_FWD`, `USD_MM`, `USD_PUT`, `USD_CALL`
  - Hedge gain/loss vs. no hedge for each strategy = Hedge Proceeds − `USD_UNHEDGED`
  - Identify the highest-yielding strategy at each `S_T` row.

---

## 5. Outputs

| Output Name          | Description                                                        | Format             | Purpose                                     |
|----------------------|--------------------------------------------------------------------|--------------------|---------------------------------------------|
| `USD_FWD`            | Locked-in USD proceeds from forward hedge                          | Single dollar value | Certainty benchmark                        |
| `USD_MM`             | USD proceeds from money market hedge                               | Single dollar value | IRP parity cross-check                     |
| `IRP_Basis`          | Difference between `USD_MM` and `USD_FWD`                         | Dollar value       | Highlight forward vs. IRP basis             |
| `PUT_Floor`          | Minimum guaranteed USD net of FV premium (put hedge)              | Single dollar value | Downside protection benchmark              |
| `Sensitivity_Table`  | USD proceeds by strategy across 21 `S_T` scenarios               | 21-row table       | Core decision-support output               |
| `HedgeGL_Table`      | Hedge gain/loss vs. no-hedge for each strategy at each `S_T`      | 21-row table       | Net benefit of hedging                     |
| `Chart_1`            | Line chart: USD proceeds by strategy vs. S_T                      | Line chart         | Visual comparison of hedge payoff profiles |
| `Summary_Table`      | One-row summary of base-case proceeds under each strategy         | 4-column table     | Executive snapshot                          |
| `Recommendation`     | Written CFO recommendation (Stage 4)                              | 1–2 paragraph text | Final deliverable narrative                |

---

## 6. Model Review — What Worked & What to Improve

### What Was Built Correctly

- The **money market hedge** logic is structurally correct: borrow in EUR at present value, convert at spot, invest at domestic rate, and the IRP-derived locked-in value is properly computed.
- The **sensitivity table framework** (varying `S_T` and computing unhedged and forward proceeds) is sound in design.
- The **no-hedge baseline** correctly computes `S_T × FC_AMT` across all scenarios.
- The **Notes sheet** clearly documents key assumptions and data sourcing conventions.

### Critical Errors Requiring Correction

1. **Wrong forward rate used.** The model computed an IRP-derived forward of **1.1993** (from spot 1.1797 and the rate differential) instead of using the scenario-given rate of **1.0935**. The forward hedge proceeds were therefore overstated by approximately **$2.1 million** (the model showed ~$23.98M vs. the correct $21.87M). This is the most material error.

2. **Option premium misidentified.** The cell labeled "EUR/USD Put Price" was set to **1.1797** (the spot rate) rather than **$0.019 per EUR** (the contract premium). As a result, the premium cost was calculated as `1.1797 × 20,000,000 = $23,594,000` — nearly the full notional value — instead of the correct `0.019 × 20,000,000 = $380,000`. This rendered all option hedge proceeds nonsensical (showing large negative values in the ~−$870K to −$24M range).

3. **Option payoff logic absent.** No conditional payoff logic was applied. The model did not distinguish between the case where `S_T < K` (put exercised, lock in floor) and `S_T ≥ K` (put expires, receive spot proceeds net of FV premium). Every scenario row showed the same negative number, which is incorrect.

4. **Call option not modeled.** The scenario specifies a EUR call (K = spot, premium = $0.024/EUR). The Stage 2 model did not include this instrument at all. The improved model should add a `USD_CALL` column in the sensitivity table for completeness, clearly labeled as non-hedging/educational.

5. **"Winner" column logic error.** Because option proceeds were negative, the winner column never correctly evaluated the put hedge; it consistently selected the money market hedge as dominant even in scenarios where the put hedge would realistically be competitive.

### Layout and Naming Improvements

- Replace the unlabeled "EUR/USD Put Price" cell with clearly named and color-coded input cells: `PREM_PUT` (blue, $0.019) and `PREM_CALL` (blue, $0.024), separated from the strike cells `K_PUT` and `K_CALL`.
- Add a dedicated **Inputs** section at the top of the model sheet with all named ranges labeled, sourced, and color-coded per financial modeling convention (blue = hardcoded input, black = formula).
- Add an **IRP Cross-Check** row that explicitly shows the IRP-implied forward alongside `F0_IN`, so the basis is transparent and intentional rather than a hidden discrepancy.
- Label the sensitivity table header "EUR/USD Spot at Maturity (S_T)" rather than "GBP/USD Spot Exchange Rate" (an apparent copy-paste error from a different scenario template).

---

## 7. Sensitivity Plan

The sensitivity analysis varies the EUR/USD spot rate at maturity (`S_T`) from **90% to 110% of S0_IN** in increments of 0.01, producing **21 scenarios**:

- **Range:** 1.0617 to 1.2977 (i.e., 0.90 × 1.1797 to 1.10 × 1.1797)
- **Step:** 0.01 USD/EUR
- **Base case row:** `S_T = S0_IN = 1.1797` (approximate mid-point)

At each `S_T`, the table computes USD proceeds for all five strategies and a gain/loss column vs. the unhedged baseline.

The accompanying line chart (`Chart_1`) plots all five strategy payoff curves on a single set of axes, making the following relationships immediately visible to the CFO:

- The **forward hedge** is a flat horizontal line — full certainty, no upside.
- The **money market hedge** is a nearly identical flat line (confirming IRP parity).
- The **unhedged** position is a rising diagonal — linear in `S_T`.
- The **EUR put hedge** is a kinked curve: flat (at the floor) when `S_T < K_PUT`, then rising in parallel with the unhedged line when `S_T > K_PUT`, offset downward by `FV_Prem_Put`.
- The **EUR call** (for reference) is a rising diagonal below the unhedged line by `FV_Prem_Call`.

The breakeven point between the put hedge and no hedge occurs where `S_T × FC_AMT − FV_Prem_Put = S_T × FC_AMT`, which simplifies to `FV_Prem_Put = 0` — meaning the put is always worse than no hedge when EUR appreciates, by exactly the cost of the premium. The breakeven between the put floor and the forward should also be identified and labeled on the chart.

---

## 8. Limitations & Next Steps

**Excluded from this model:**
- Implied volatility and dynamic option pricing (Black-Scholes); the premium is treated as a fixed given.
- Credit and counterparty risk on the forward contract or money market borrowing.
- Accounting treatment (ASC 815 hedge designation, effectiveness testing).
- Dynamic re-hedging or rolling strategies.
- Transaction costs, bid-ask spreads, and financing fees.
- Tax implications of realized hedge gains or losses.

**Next steps (Stage 4):**
This specification serves as the primary input document for the Stage 4 AI-assisted model build. The named ranges, corrected calculation flow, and explicit output requirements defined here will be translated directly into an AI prompt that instructs reconstruction and enhancement of the model. The Stage 4 deliverable will incorporate the corrected forward rate, accurate option premium logic, conditional put payoff formulas, and a CFO-ready written recommendation based on the improved sensitivity results.
