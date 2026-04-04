# FX Receivable Exposure Memo – Scenario 4

Created by: Jazen  
Updated by: Jazen  
Date Created: 2026-04-03  
Date Updated: 2026-04-03  
Version: 
LLM Used: GPT-5.4 

## Executive Summary

Our firm has foreign exchange transaction exposure because it expects to receive **€20,000,000 in one year** and will ultimately convert those proceeds into U.S. dollars. For Stage 1, I am using a current EUR/USD spot reference of **1.1545 USD/EUR** and setting the option strike **K** at the current spot, which is consistent with a basic at-the-money hedge setup. If the euro weakens over the next year, the dollar value of the receivable will decline, reducing actual USD proceeds and creating budgeting and earnings uncertainty. A hedge is worth considering because it can protect the firm from adverse exchange-rate moves while improving cash flow visibility. The main hedge families to evaluate are a forward hedge, a money market hedge, and an option hedge. In the next stages, I will build an Excel model using market-close inputs, compare hedge outcomes across future exchange-rate scenarios, and recommend the most appropriate strategy to the CFO.  

## Background & Objectives

The company is a U.S. aerospace manufacturer with a large euro-denominated receivable due in one year. Since the firm reports in USD, its exposure is the risk that the euro depreciates before collection. Even a modest change in EUR/USD can materially affect the final dollar proceeds on a €20 million inflow.

Based on current market-close style inputs for this project, I will use:  
- **Spot EUR/USD:** 1.1545  
- **Forward rate (1 year):** 1.0935  
- **USD interest rate proxy:** 3.625%  
- **EUR interest rate proxy:** 2.00%  
- **Option strike (K):** 1.1545  

The objective is to explain the receivable exposure clearly and identify practical hedge families before selecting a final strategy.  

## Methods

I will compare three hedge families:

1. **Forward hedge**  
   Lock in the 1-year forward rate of **1.0935 USD/EUR**.  
   **Pros:** simple, transparent, and fully protects against euro depreciation.  
   **Cons:** eliminates upside if the euro strengthens.

2. **Money market hedge**  
   Use euro and dollar interest rates to create a synthetic locked-in USD value today.  
   **Pros:** provides certainty and is useful for comparing theoretical hedge value.  
   **Cons:** requires more setup and depends on the borrowing and investing rate assumptions used.

3. **Option hedge**  
   Buy a **put on EUR** with strike **1.1545** and premium **$0.019 per euro**.  
   **Pros:** protects downside while preserving upside if EUR rises above the strike.  
   **Cons:** requires an upfront premium, which lowers net proceeds.

## Limitations & Next Steps

This memo uses current market-close proxy inputs for Stage 1 and assumes those same types of inputs will be formally recorded on the day Stage 2 begins. Actual hedge results will depend on the final spot, selected strike, and chosen interest-rate proxies at that time.

Next, I will build an Excel model that compares unhedged, forward, money market, and option outcomes, document the model structure in a technical specification, and use those results to make a final recommendation to the CFO.

## References

Yahoo Finance. (2026, April 3). *EUR/USD (EURUSD=X) historical data*.  
Board of Governors of the Federal Reserve System. (2026, March 18). *FOMC statement*.  
Federal Reserve Bank of St. Louis. (2026, April 2). *ECB Deposit Facility Rate for Euro Area (ECBDFR)*.
