# OIL & GAS VALUATION AGENT SPECIFICATION

## Adapted from Don Durrett Mining Agent v2.2

---

# OUTPUT SPECIFICATION FOR OIL & GAS AGENT

[OUTPUT SPECIFICATION – REQUIRED; NO OVERRIDE]
The agent's response MUST exactly follow this Markdown skeleton in this order:

## Report Header (required)
- ISO Date: YYYY-MM-DD
- Data Quality Score: overall & breakdown by factor
- Primary Sources: list citations

## Executive Summary (required)
(2–3 concise paragraphs; include DQS caveats & final Recommendation)

## Property List (required)
| Property      | Jurisdiction  | Status       | WI/NRI           |
|---------------|----------------|--------------|------------------|
| …             | …              | …            | …                |

## Proved & Probable Reserves (required)
| Property        | Proved Oil (Mbbl) | Proved Gas (Bcf) | Probable Oil (Mbbl) | Probable Gas (Bcf) |
|-----------------|-----------------|-----------------|---------------------|-------------------|
| …              | …               | …               | …                   | …                 |

## Resource Calculation (required)
- Show step-by-step calculation of total resource value using BOE (barrels of oil equivalent).

## 10-Factor Analysis (required - adapted for E&P)
| Factor                         | Score | Rating  | Notes                     | Sources |
|--------------------------------|-------|---------|---------------------------|---------│
| Properties & Acreage           |       |         | One-sentence rationale     | [n]     |
| Management & Track Record      |       |         | One-sentence rationale     | [n]     |
| …                              |       |         | …                         | [n]     |

## Radar Chart (required)
- Invoke createchartradar tool; embed resulting image tag here.
 
## Company Valuation (required)
- NPV (10% discount rate, 10-year horizon): $…
- EV/BOE: $…; Breakeven price: $…
- Implied Upside: X% 🚀

## Red Flags (required if any score < 6)
- Factor name: brief concern

## Data Quality Audit (required)
- Note missing inputs or assumptions.

## Investor Concerns (required)
- List execution risks, funding gaps, reserve decline.

## Audit Trail (required)
```
{
  "source_citations": […],
  "token_usage": {"prompt":…, "completion":…},
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "model_version": "claude-sonnet-3.5",
  "sec_filing_date": "…",
  "reserve_audit_date": "…"
}
```

## Disclaimer (required)
This report is for informational purposes only. Not financial advice.
[END OUTPUT SPECIFICATION]

---

# CORE FORMULA LIBRARIES FOR OIL & GAS

## Constants
- `proved_weight = 1.0`
- `probable_weight = 0.75` (conservative; use 1.0 for 2P baseline)
- `possible_weight = 0.5` (3P upside scenario only)
- `boe_conversion = 6` (1 barrel oil = 6 Mcf gas)
- `resource_valuation_percentage = 0.10`

## Reserve & Resource Calculations

### Plausible Resources (BOE-adjusted)
```
plausible_resources_boe = (proved_oil + (proved_gas / 6)) × proved_weight +
                          (probable_oil + (probable_gas / 6)) × probable_weight +
                          (possible_oil + (possible_gas / 6)) × possible_weight

proved = (proved_oil + (proved_gas / 6))
probable = (probable_oil + (probable_gas / 6))
possible = (possible_oil + (possible_gas / 6))
total_1p = proved
total_2p = proved + probable
total_3p = proved + probable + possible
```

### Fully Diluted Shares (same as mining)
```
fully_diluted_shares = shares_outstanding + options_warrants + convertible_securities
```

### Net Present Value (Oil & Gas Specific)
```
npv = Σ(annual_cash_flow_t / (1 + discount_rate)^t) - initial_capex

annual_cash_flow = (production_boe × realized_price) - operating_costs - severance_taxes
discount_rate = 0.10 (industry standard; SEC mandated PV-10)
evaluation_period = 10 years
```

### Breakeven Price Calculation
```
breakeven_price = (total_capex + fixed_opex_per_boe) / total_boe_produced

Where:
- total_capex = capital expenditure to develop property
- fixed_opex_per_boe = fixed costs amortized per BOE
- variable_opex_per_boe = lease operating expenses (LOE)
```

### Free Cash Flow (Oil & Gas)
```
fcf = (production_boe × realized_price) - operating_costs - royalties - taxes - capex
fcf_margin_per_boe = realized_price - breakeven_price

Where:
- production_boe = annual production in barrels of oil equivalent
- realized_price = net revenue per BOE (after hedging, take)
- operating_costs = lease operating expenses + G&A
- royalties = working interest × royalty rate
- taxes = severance + income taxes
```

### Enterprise Value
```
enterprise_value = market_cap + net_debt
net_debt = total_debt - cash

ev_per_boe = enterprise_value / total_1p_reserves_boe
ev_per_mcf_gas = enterprise_value / proved_gas_bcf
```

### Resource Value Ratio
```
reserve_value_ratio = (reserve_boe × oil_price × resource_valuation_pct) / market_cap
```

## Production Decline & Reserve Replacement

### Reserve Replacement Ratio (RRR)
```
rrr = new_reserves_added / annual_production
rrr < 1.0 = declining reserves (concern)
rrr > 1.0 = reserve growth (positive)
```

### Production Decline Rate (yearly)
```
annual_decline_pct = (production_year_1 - production_year_2) / production_year_1 × 100
typical_decline_rate = 8-15% annually (depends on property maturity)
```

### Reserve Life Index (RLI)
```
reserve_life_index = total_1p_reserves / annual_production
rli_years = years of production at current rate
threshold: RLI < 5 years = high replacement risk
```

## Data Quality Score (DQS)
```
data_quality_score = (accuracy × 0.5) +
                    (hallucination × 0.3) +
                    (sycophancy × 0.2)
```

## Risk Assessment Formulas

### Commodity Price Risk Score
```
function get_commodity_risk_score(vol_oil_pct, vol_gas_pct, hedge_pct):
    unhedged_exposure = 1 - hedge_pct
    blended_volatility = (vol_oil_pct × 0.6) + (vol_gas_pct × 0.4)  # typical oil-heavy
    risk_score = blended_volatility × unhedged_exposure
    return risk_score
```

### Development Timeline Risk
```
timeline_risk_score = 1 - (years_to_first_production / 5.0)
if years < 1: score = 1.0 (low risk)
if years 1-3: score = 0.7
if years 3-5: score = 0.4
if years > 5: score = 0.1 (high risk)
```

### Regulatory/Jurisdictional Risk
```
function get_oil_gas_jurisdiction_risk(country):
    tier_1_low = ["Canada", "USA", "Australia", "Norway"]  // Risk = 1
    tier_2_mod = ["Mexico", "Brazil", "UK North Sea"]      // Risk = 2
    tier_3_high = ["Russia", "Venezuela", "Nigeria"]       // Risk = 4
    
    if country in tier_1_low: return 1
    elif country in tier_2_mod: return 2
    else: return 4
```

---

## Validation Rules (Oil & Gas Adapted)

```
validation_rules:
  - rule: primary_source_verification
    requirement: "All reserve figures from SEC 10-K Item 1A (Supplemental Oil & Gas Info) or equivalent"
    failure_action: ["USE_BEST_ESTIMATE", "FLAG_ESTIMATE"]
  
  - rule: reserve_logic_check
    requirement: "1P ≤ 2P ≤ 3P reserves (monotonic ordering)"
    failure_action: "FLAG_FOR_REVIEW"
  
  - rule: price_assumption_currency
    requirement: "All oil prices in USD/bbl, gas in USD/Mcf, consistent with filing date"
    failure_action: "USE_FILING_PRICE_ASSUMPTION"
  
  - rule: reserve_dating
    requirement: "Reserves must be ≤ 18 months old from current date"
    failure_action: "FLAG_OUTDATED_RESERVES"
```

---

## Output Validation for E&P

```markdown
# Required Outputs Checklist
- [ ] NPV calculated using 10% discount rate, 10-year horizon
- [ ] BOE conversions consistent (1 barrel = 6 Mcf)
- [ ] Reserve life index calculated (years of reserves at current production)
- [ ] RRR (reserve replacement ratio) disclosed
- [ ] Commodity exposure and hedge ratio documented
- [ ] Regulatory/jurisdictional risk score included
- [ ] DQS calculated and breakdown provided
- [ ] All SEC/regulatory source citations included
- [ ] Breakeven price vs. current/forward prices analyzed
```

---

# NOTES ON RESERVE WEIGHTING

## Industry Standard vs. Durrett Approach

The Don Durrett mining methodology uses **plausible resources weighting**:
- Proved + Probable (P&P): 1.0
- Measured + Indicated (M&I): 0.75
- Inferred: 0.5

For **oil & gas**, adapt conservatively:

### Recommended Weights
- **1P (Proved)**: 1.0 (≥90% probability)
- **2P (Probable)**: 0.75–1.0 (≥50% probability)
  - Use 1.0 for 2P baseline valuation (industry standard)
  - Use 0.75 for conservative/downside scenario
- **3P (Possible)**: 0.5 (≥10% probability; upside scenario only)

### Rationale
- Industry standard is **2P reserves** for M&A, lending, and investor presentations
- 3P inclusion (full weight) inflates NPV by 20-50%+ vs. 2P baseline, risking poor decisions
- SEC mandates 1P for official filings; 2P is optional but widely accepted

### Best Practice
Use **2P baseline (proved + probable × 1.0)** for core valuation; model 3P as upside sensitivity scenario only.

---

# KEY DIFFERENCES FROM MINING TO E&P

| Aspect | Mining (Don Durrett) | Oil & Gas (Your Agent) |
|--------|---------------------|----------------------|
| **Reserve Classes** | P&P, M&I, Inferred | 1P, 2P, 3P |
| **Valuation Unit** | $/oz | $/BOE, $/Mcf |
| **Key Formula** | NAV multiples × stage | NPV with 10% DCF |
| **Production Risk** | Mines have long ramp-up | Wells decline 8-15%/year |
| **Reserve Replacement** | Exploration upside | RRR and decline rates critical |
| **Price Assumptions** | Current spot prices | SEC filing price assumptions + forward curves |
| **Financials** | AISC per oz | LOE, severance taxes, royalties |

---

# EXAMPLE OUTPUT STRUCTURE

## Report Header
**Company Name (Ticker) - E&P Valuation Analysis**
- **Analysis Date**: 2026-01-16
- **Data Quality Score**: 8.2/10 (A:8.5 H:7.8 S:8.2)
- **Source Confidence**: 90% Primary Sources

## Executive Summary
[2-3 paragraphs with investment thesis, key risks, final recommendation]

## Property List
[Table with Property, Jurisdiction, Status, WI/NRI]

## Reserves Table
[1P, 2P, 3P breakdown by property in Mbbl oil / Bcf gas]

## Plausible Resources Calculation
```
1P (Proved): 45.0 Mbbl oil + (8.2 Bcf / 6) = 46.4 MBOE × 1.0 = 46.4 MBOE
2P (Probable): 18.0 Mbbl + (3.5 Bcf / 6) = 18.6 MBOE × 1.0 = 18.6 MBOE
Total 2P: 64.9 MBOE

3P (Possible): 12.0 Mbbl + (2.1 Bcf / 6) = 12.4 MBOE × 0.5 = 6.2 MBOE
Total Plausible: 71.1 MBOE
```

## 10-Factor Analysis
[Table with scores, ratings, rationale for each factor]

## Company Valuation
- **NPV (10% DCF, 10yr)**: $125M
- **EV/BOE**: $8.50
- **Breakeven Price**: $42/bbl
- **Implied Upside**: 45% 🚀

---

**Disclaimer**: This report is for informational purposes only. Not financial advice.

---

## INTEGRATION CHECKLIST

- [ ] Copy output specification into system prompt
- [ ] Implement formula library in Python/calculation engine
- [ ] Adapt 10-factor definitions for E&P (see original Don Durrett agent)
- [ ] Set up data validation rules for SEC 10-K/10-Q filings
- [ ] Configure DQS (Data Quality Score) audit module
- [ ] Test with 2-3 public E&P companies (EOG, CVX, COP)
- [ ] Validate NPV calculations against Bloomberg/IHS estimates
- [ ] Document all citations to SEC Edgar and company filings
