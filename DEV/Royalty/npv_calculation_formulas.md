
# NPV CALCULATION FORMULAS FOR ROYALTY/STREAMING COMPANIES

## 1. ATTRIBUTABLE PRODUCTION CALCULATION

### For Royalty Interests (NSR, GRR, GVR):
Attributable_Ounces = Gross_Production × (Royalty_Rate_Percent / 100)

### For Streaming Agreements:
Attributable_Ounces = Gross_Production × (Stream_Percentage / 100)

### For Net Profits Interest (NPI):
Attributable_Value = (Gross_Revenue - Operating_Costs - Capital_Costs) × (NPI_Rate_Percent / 100)


## 2. ANNUAL CASH FLOW CALCULATION

### For Royalty Interests:
Annual_Cash_Flow = Attributable_Ounces × Metal_Price

### For NSR (Net Smelter Return) - if cost deductions apply:
Annual_Cash_Flow = (Gross_Revenue - Smelting_Costs - Refining_Costs - Transport_Costs) × (NSR_Rate / 100)

### For Streaming Agreements:
Annual_Revenue = Attributable_Ounces × Spot_Metal_Price
Ongoing_Payment = Attributable_Ounces × Per_Ounce_Payment
Annual_Cash_Flow = Annual_Revenue - Ongoing_Payment

### With Payability Adjustment (typically 95-98% for gold):
Deliverable_Ounces = Attributable_Ounces × Payability_Percent


## 3. DISCOUNT RATE CALCULATION

Total_Discount_Rate = Base_Rate + Jurisdiction_Risk_Premium + Stage_Risk_Premium

### Base Rates by Commodity:
- Precious Metals (Au, Ag): 6-8%
- Base Metals (Cu, Zn, Pb): 10%+
- Mixed Precious/Base: 8%
- Oil & Gas: 10-12%

### Jurisdiction Risk Premium:
- Low Risk (Canada, Australia, USA, Chile): +0%
- Moderate Risk (Mexico, Brazil, Peru): +2-3%
- High Risk (DRC, Tanzania, certain African nations): +5-7%
- Very High Risk (recent nationalization history): +7%+

### Development Stage Risk Premium:
- Producing: +0%
- Development: +2-3%
- Advanced Exploration: +4%
- Exploration: +5%

Example: Gold royalty in Brazil (producing) = 7% base + 2% jurisdiction + 0% stage = 9% total


## 4. PRESENT VALUE FACTOR CALCULATION

PV_Factor_Year_N = 1 / (1 + Discount_Rate)^N

Where N = Number of years from present (Year 1 = first year of cash flow)

Example: 
Year 1 at 8% discount rate: PV_Factor = 1 / (1.08)^1 = 0.9259
Year 10 at 8% discount rate: PV_Factor = 1 / (1.08)^10 = 0.4632


## 5. DISCOUNTED CASH FLOW CALCULATION

Discounted_CF_Year_N = Annual_Cash_Flow_Year_N × PV_Factor_Year_N


## 6. NET PRESENT VALUE (NPV) CALCULATION

NPV = Σ(Discounted_CF_Year_1 to Year_N) - Initial_Investment

For royalty companies that don't bear initial investment:
NPV = Σ(Discounted_CF_Year_1 to Year_N)

Or in expanded form:
NPV = CF₁/(1+r)¹ + CF₂/(1+r)² + CF₃/(1+r)³ + ... + CFₙ/(1+r)ⁿ

Where:
- CF = Cash Flow for each year
- r = Discount rate (as decimal, e.g., 0.08 for 8%)
- n = Year number


## 7. INTERNAL RATE OF RETURN (IRR)

IRR is the discount rate where NPV = 0

0 = -Initial_Investment + Σ(CF_Year_N / (1 + IRR)^N)

Solve iteratively for IRR. For acquisitions:
IRR should exceed hurdle rate (typically 15-25% for mining projects)


## 8. PAYBACK PERIOD

Payback_Period = Year when Cumulative_Undiscounted_Cash_Flow >= Initial_Investment


## 9. WEIGHTED AVERAGE LIFE OF ASSET

WAL = Σ(Year × Discounted_CF_Year) / Σ(Discounted_CF_Year)

This represents the NPV-weighted average duration of cash flows.


## 10. NAV PER SHARE CONTRIBUTION

NAV_Per_Share_Contribution = Asset_NPV / Total_Shares_Outstanding


## 11. GOLD EQUIVALENT OUNCE (GEO) CONVERSION

GEO = Gold_Ounces + (Silver_Ounces × Silver_Price / Gold_Price) + 
                    (Copper_Pounds × Copper_Price × Conversion_Factor / Gold_Price)

Example conversion factors:
- Silver to Gold: Divide by gold/silver ratio (typically 1:80-90)
- Copper to Gold: 1 lb copper ≈ 0.00133 oz gold equivalent at $4.00/lb Cu and $3000/oz Au


## 12. SENSITIVITY ANALYSIS

NPV_at_Price_Plus_10% = Recalculate NPV with metal prices increased by 10%
NPV_at_Production_Minus_10% = Recalculate NPV with production decreased by 10%

Sensitivity_Ratio = (NPV_at_Sensitivity - Base_Case_NPV) / Base_Case_NPV


## 13. TERMINAL VALUE (If applicable)

### Perpetuity Growth Method:
Terminal_Value = CF_Final_Year × (1 + g) / (r - g)

Where:
- g = perpetual growth rate (typically 0-2%)
- r = discount rate

### Multiple Method:
Terminal_Value = Final_Year_Cash_Flow × Exit_Multiple

Common multiples: 5-10x annual cash flow


## 14. EFFECTIVE MARGIN CALCULATION (for Streams)

Effective_Margin = (Spot_Price - Ongoing_Payment) / Spot_Price × 100%

Example:
Gold stream with $450/oz ongoing payment at $3200/oz gold:
Margin = ($3200 - $450) / $3200 = 85.9%


## 15. MINE LIFE CALCULATION (if not stated)

Mine_Life_Years = Total_Reserves / Annual_Production_Rate

Or from reserve data:
Mine_Life = (Proven_Reserves + Probable_Reserves) / (Annual_Throughput × Recovery_Rate × Grade)


## VALIDATION CHECKS

1. NPV Relationship Check:
   NPV_5% > NPV_8% > NPV_10% (lower discount rates yield higher NPVs)

2. Mine Life Consistency:
   Production_Forecast_Duration ≈ Stated_Mine_Life (within 2 years)

3. Discount Rate Sum:
   Total_Discount_Rate = Base_Rate + Jurisdiction_Premium + Stage_Premium

4. GEO Revenue Check:
   Implied_Gold_Price = Revenue / GEO_Ounces
   (Should be within reasonable range: $1500-5000/oz)

5. Cash Flow Sign Check:
   All annual cash flows should be positive for producing assets

6. IRR Threshold:
   IRR > Discount_Rate for value-creating investments
   IRR > 15% typically required for new acquisitions
