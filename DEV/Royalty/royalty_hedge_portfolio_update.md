# Royalty Hedge Portfolio Update Microservice

**Version:** 1.0  
**Type:** Microservice (Markdown-based specification)  
**Purpose:** Dynamically detect and populate hedge portfolio JSON for ANY oil & gas royalty company that has an active hedging program  
**Scope:** Generic - no hardcoded company data  

---

## Service Overview

This microservice is designed to be called as part of a larger quarterly royalty company data refresh workflow. It:

1. **Detects** whether a company has an active commodity hedging program, if it does not, the rest of the program can be skipped.
2. **Extracts** hedge specifications from SEC filings (10-Q/10-K)
3. **Validates** extracted data against known hedge patterns
4. **Populates** a JSON hedge portfolio with all relevant fields
5. **Outputs** a standardized JSON record ready for RAG or analytics pipeline

---

## Input Requirements

The microservice expects the following inputs:

```yaml
Input:
  company_ticker: string (e.g., "BSM", "KRP", "FRU")
  company_name: string (full legal name)
  as_of_date: date (quarter end: YYYY-MM-DD)
  sec_filing_url: string (direct link to 10-Q filing on SEC EDGAR)
  reporting_quarter: string (e.g., "Q2 2025")
```

---

## Exit Control: Early Termination logic


exit_control:
version: "1.0"
trigger_point: "BEFORE_DETECTION_PHASE"

# Check if hedging program exists
has_active_hedging_program: null # Set by Detection Phase

# Exit conditions that halt workflow
exit_conditions:
- condition: "has_active_hedging_program == false"
  action: "STOP"
  skip_phases: ["EXTRACTION", "VALIDATION", "RED_FLAG_DETECTION", "OUTPUT"]
  return_message: "Hedging program not applicable - workflow terminated"

- condition: "has_active_hedging_program == true"
  action: "CONTINUE"
  next_phase: "EXTRACTION_PHASE"

```
# Response when exiting
  exit_response:
  company_ticker: "{input_ticker}"
  has_active_hedging_program: false
  workflow_status: "TERMINATED"
  reason: "Company policy excludes commodity hedging"
  termination_timestamp: "{current_datetime}"
```

**Workflow Logic:**
```
IF exit_control.exit_conditions matches:
→ Return exit_response JSON
→ DO NOT process sections: Extraction, Validation, Red Flags, Output
→ Stop workflow

IF exit_control.exit_conditions matches:​
→ Continue to Detection Phase
→ Proceed with normal extraction workflow
```


---

## Detection Phase: Does This Company Hedge?

The service first determines if the company has an active hedging program by scanning for:

### Positive Indicators (HEDGING ACTIVE)
- [ ] MD&A section contains "Risk Management" or "Commodity Price Risk" subsection
- [ ] Table showing hedge volumes (oil, gas, or both)
- [ ] Strike prices disclosed for any forward period
- [ ] Fair value measurements in notes to financial statements
- [ ] Realized/unrealized gain/loss line items in P&L
- [ ] Counterparty names (swap dealers) listed
- [ ] Settlement method descriptions (monthly, quarterly, etc.)

### Negative Indicators (NO HEDGING / POLICY EXCLUSION)
- [ ] MD&A explicitly states "does not enter into hedging activities"
- [ ] "Company does not anticipate entering into financial hedging"
- [ ] Zero volumes in any hedge table
- [ ] No derivative asset/liability on balance sheet
- [ ] No fair value disclosures for derivatives

### Output of Detection Phase
```json
{
  "company_ticker": "input_ticker",
  "has_active_hedging_program": true_or_false,
  "detection_confidence": "HIGH|MEDIUM|LOW",
  "hedging_status": "ACTIVE|INACTIVE|POLICY_EXCLUSION|UNKNOWN",
  "reason": "Explicit statement or implicit indicators found"
}
```

---

## Extraction Phase: Collect Hedge Data

If `has_active_hedging_program == true`, proceed with extraction.

### Step 1: Locate Hedge Tables in MD&A

**Search locations:**
- Item 7A (Quantitative and Qualitative Disclosures About Market Risk)
- Item 3A (for some older formats)
- "Risk Management" subsection of MD&A
- "Commodity Price Risk" subsection

**Table structure to identify:**
```
| Commodity    | Period      | Volume (Units) | Weighted Avg Strike | Strike Low | Strike High |
|--------------|-------------|----------------|---------------------|------------|------------|
| WTI Oil      | H2 2025     | 555,000 BBL    | $71.22              | $68.26     | $74.20     |
| Henry Hub    | Q4 2025     | 10,800 BBtu    | $3.36               | $3.25      | $3.50      |
```

### Step 2: Extract Hedge Specifications

For **EACH hedge position** found:

```yaml
Hedge Instance Record:
  hedge_id: "{TICKER}-{COMMODITY}-{PERIOD}-{INDEX}"
  
  Identification:
    company_ticker: "from input"
    company_name: "from input"
    commodity: "WTI_CRUDE_OIL | NYMEX_NATURAL_GAS | BRENT_CRUDE_OIL | HH_NATURAL_GAS"
    commodity_unit: "BBL | MMBtu | BBtu"
    
  Duration:
    reporting_quarter: "from input"
    as_of_date: "from input"
    period_covered: "extract from table (e.g., H2 2025, Full Year 2026)"
    start_date: "infer from period (e.g., 2025-07-01 for H2 2025)"
    end_date: "infer from period (e.g., 2025-12-31 for H2 2025)"
    
  Volume:
    total_volume: "from table (numeric only)"
    volume_unit: "from table header or infer"
    
  Pricing:
    weighted_avg_strike: "from table"
    strike_price_low: "from table"
    strike_price_high: "from table"
    
  Hedge Structure:
    hedge_type: "FIXED_PRICE_SWAP | COSTLESS_COLLAR | PUT_OPTION | OTHER (infer from description)"
    settlement_method: "MONTHLY_CASH | QUARTERLY | FIRST_NEARBY_FUTURES (from MD&A description)"
```

### Step 3: Extract Fair Value Data

**Location:** Notes to Financial Statements → Derivatives and Hedging / Fair Value

**Extract:**
```yaml
Fair Value:
  fair_value_beginning_period: "from notes table"
  mark_to_market_gain_loss: "change in fair value this quarter"
  fair_value_end_period: "from notes table (end of Q)"
  valuation_level: "Level 1 | Level 2 | Level 3"
```

### Step 4: Extract Settlement Activity

**Location:** MD&A Risk Management section (narrative or table)

**Extract:**
```yaml
Realized vs Unrealized:
  realized_pnl_current_quarter: "cash gains/losses on settlements this Q"
  unrealized_pnl_current_quarter: "mark-to-market changes this Q"
  total_pnl_current_quarter: "realized + unrealized"
```

### Step 5: Extract Pricing & Risk Context

**From MD&A or footnotes:**
```yaml
Pricing Context:
  current_spot_price: "current market price for commodity (if disclosed)"
  spot_vs_strike: "current spot - weighted avg strike (calculate)"
  
Risk Context:
  counterparties: "list of swap dealers (e.g., [Goldman Sachs, JP Morgan])"
  basis_risk_present: "true if hedge index != actual sales price"
```

### Step 6: Infer Hedge Rules & Structure

**Based on extracted data, infer:**

```yaml
Hedge Rules:
  is_costless_collar: 
    condition: "IF (strike_low + strike_high) / 2 ≈ prior quarter spot THEN true"
    reason: "Costless collars structured with symmetric range"
  
  structure_description: 
    IF hedge_type == FIXED_PRICE_SWAP:
      "Company receives/pays difference between {strike} and settlement price, settled monthly"
    IF hedge_type == COSTLESS_COLLAR:
      "Downside protected at {strike_low}, capped at {strike_high}"
  
  cost_of_hedge:
    embedded_cost_per_unit: "spot at signing - weighted_avg_strike"
    cost_type: "EMBEDDED_DEALER_SPREAD | ZERO_COST_COLLAR (infer from structure)"
```

---

## Validation Phase: Sanity Checks

Before outputting JSON, perform validation checks:

### Check 1: Volume Consistency
```
Validation:
  Rule: total_volume > 0
  Rule: volume_unit matches commodity (BBL for oil, MMBtu for gas)
  Rule: average_monthly_volume = total_volume / duration_months
  
  Action if FAIL: Flag as "VALIDATION_ERROR" and request manual review
```

### Check 2: Price Consistency
```
Validation:
  Rule: weighted_avg_strike is between strike_low and strike_high
  Rule: strike_low > 0 and strike_high > 0
  Rule: strike_low ≤ weighted_avg_strike ≤ strike_high
  
  Action if FAIL: Flag and alert
```

### Check 3: Fair Value Sign
```
Validation:
  Rule: If current_spot < weighted_avg_strike:
        unrealized_pnl should be NEGATIVE (company underwater)
  Rule: If current_spot > weighted_avg_strike:
        unrealized_pnl should be POSITIVE (company in-the-money)
  
  Action if FAIL: Investigate reconciliation
```

### Check 4: P&L Composition
```
Validation:
  Rule: |realized_pnl| ≤ |total_pnl|
  Rule: unrealized_pnl = total_pnl - realized_pnl
  
  Action if FAIL: Recalculate or request confirmation
```

---

## Red Flag Detection Phase

As data is extracted, automatically detect and FLAG:

```yaml
Red Flags to Check:

1. DECLINING_STRIKE_LADDER:
   Condition: weighted_avg_strike this Q < prior Q strike
   Severity: HIGH (indicates potential distribution pressure)
   Action: Add flag to hedge record

2. FAIR_VALUE_DETERIORATION:
   Condition: fair_value_end_period < fair_value_beginning_period - $10M
   Severity: MEDIUM (mark-to-market swings)
   Action: Add flag

3. REALIZED_PNL_NEGATIVE:
   Condition: realized_pnl_current_quarter < 0
   Severity: MEDIUM (cash outflows from hedges)
   Action: Add flag, assess duration

4. COUNTERPARTY_RISK:
   Condition: Any counterparty not investment grade
   Severity: HIGH (credit risk)
   Action: Add flag, note counterparty names

5. COVERAGE_LOW:
   Condition: pct_of_annual_production < 0.05 (less than 5%)
   Severity: LOW (minimal protection)
   Action: Add note
```

---

## Output Specification

Upon successful extraction and validation, output a **single hedge portfolio JSON record**:

```json
{
  "hedge_id": "generated from {TICKER}-{COMMODITY}-{PERIOD}-001",
  "company_ticker": "input_ticker",
  "company_name": "input_name",
  "commodity": "WTI_CRUDE_OIL | NYMEX_NATURAL_GAS | etc.",
  "commodity_unit": "BBL | MMBtu",
  "hedge_type": "FIXED_PRICE_SWAP | COSTLESS_COLLAR | PUT_OPTION | OTHER",
  
  "hedge_rules": {
    "structure_description": "Plain English description",
    "is_costless_collar": boolean,
    "swap_fixed_price": number,
    "collar_floor_price": number_or_null,
    "collar_ceiling_price": number_or_null,
    "settlement_method": "MONTHLY_CASH | QUARTERLY | FIRST_NEARBY_FUTURES",
    "index_used": "WTI_CALENDAR_MONTH_AVG | NYMEX_HENRY_HUB_FIRST_NEARBY",
    "counterparties": ["dealer1", "dealer2"]
  },
  
  "cost_of_hedge": {
    "upfront_cost_dollars": 0,
    "embedded_cost_per_unit": number_or_null,
    "embedded_cost_pct": number_or_null,
    "total_embedded_cost_dollars": number_or_null,
    "cost_type": "ZERO_COST_COLLAR | EMBEDDED_DEALER_SPREAD | OTHER"
  },
  
  "as_of_date": "YYYY-MM-DD",
  "reporting_quarter": "Q# YYYY",
  "duration": {
    "start_date": "YYYY-MM-DD",
    "end_date": "YYYY-MM-DD",
    "duration_months": integer,
    "coverage_period_description": "H2 2025 (July-December)"
  },
  
  "volume": {
    "total_volume": number,
    "volume_unit": "BBL | MMBtu",
    "average_monthly_volume": number,
    "pct_of_annual_production": number_0_to_1
  },
  
  "pricing": {
    "weighted_avg_strike": number,
    "strike_price_low": number,
    "strike_price_high": number,
    "strike_price_range": "string_format",
    "current_spot_price": number_or_null,
    "spot_vs_strike": number_or_null,
    "in_the_money": boolean_or_null
  },
  
  "fair_value": {
    "fair_value_beginning_period": number_or_null,
    "mark_to_market_gain_loss": number_or_null,
    "fair_value_end_period": number_or_null,
    "valuation_level": "LEVEL_1 | LEVEL_2 | LEVEL_3"
  },
  
  "pnl": {
    "realized_pnl_current_quarter": number_or_null,
    "unrealized_pnl_current_quarter": number_or_null,
    "total_pnl_current_quarter": number_or_null
  },
  
  "risk_flags": [
    {
      "flag_type": "DECLINING_STRIKE_LADDER | FAIR_VALUE_DETERIORATION | etc.",
      "severity": "HIGH | MEDIUM | LOW",
      "description": "narrative"
    }
  ],
  
  "data_quality": {
    "source_document": "10-Q | 10-K",
    "extraction_method": "MANUAL | AUTOMATED | HYBRID",
    "confidence_level": "HIGH | MEDIUM | LOW",
    "validation_status": "PASSED | WARNINGS | FAILED",
    "notes": "Any caveats or issues encountered"
  }
}
```

---

## Workflow Integration

### Call Signature
```
CALL royalty_hedge_portfolio_update(
  company_ticker: string,
  company_name: string,
  as_of_date: date,
  sec_filing_url: string,
  reporting_quarter: string
)

RETURNS:
  IF has_active_hedging:
    hedge_portfolio_json (single record or array)
  ELSE:
    {
      "company_ticker": ticker,
      "has_active_hedging": false,
      "reason": "explicit policy or no indicators found"
    }
```

### Integration Points
- **Upstream:** Receives company ticker, quarter, SEC URL from royalty company quarterly refresh workflow
- **Downstream:** Outputs JSON record(s) to be appended to `royalty_hedge_master_database.json`
- **Error Handling:** Returns validation errors for manual review; never outputs incomplete data

---

## Processing Rules

### Rule: Generic Company Detection
```
Do NOT hardcode company names or tickers.
Accept ANY company_ticker and company_name as input.
Apply hedge detection logic universally.
Output JSON structure is identical regardless of company.
```

### Rule: Multiple Hedge Positions
```
If a company has multiple hedges (e.g., oil Q4 2025, oil 2026, gas Q4 2025):
Output MULTIPLE JSON records (one per hedge position).
Each record gets a unique hedge_id (incremented INDEX).

Example:
  BSM-OIL-H2_2025-001
  BSM-OIL-FY_2026-002
  BSM-GAS-H2_2025-003
```

### Rule: Missing Data Handling
```
For any field:
  IF data not found in filing:
    Set to null (do NOT use defaults or estimates)
  IF data is confidential:
    Set to null with note in data_quality.notes
  IF data contradictory:
    Flag validation error and request manual review
```

### Rule: Date Inference
```
Period inputs like "H2 2025" or "Full Year 2026":
  H2 2025 → start: 2025-07-01, end: 2025-12-31
  Q4 2025 → start: 2025-10-01, end: 2025-12-31
  Full Year 2026 → start: 2026-01-01, end: 2026-12-31
  H1 2027 → start: 2027-01-01, end: 2027-06-30
```

---

## Implementation Notes

### Technology Stack
- **Input parsing:** YAML or JSON
- **Extraction:** SEC EDGAR API + PDF/HTML parsing
- **Validation:** JSON Schema validation
- **Output:** Structured JSON

### Error Scenarios
| Scenario | Action |
|----------|--------|
| SEC filing not found | Return error, request manual URL verification |
| Hedge table malformed | Flag confidence as LOW, request manual verification |
| Fair value numbers missing | Set to null, add flag |
| Conflicting data sources | Flag and request manual reconciliation |
| Company has no hedges | Return `has_active_hedging: false` with reason |

---

## Example Execution

### Input
```yaml
company_ticker: "BSM"
company_name: "Black Stone Minerals, L.P."
as_of_date: "2025-09-30"
sec_filing_url: "https://www.sec.gov/Archives/edgar/data/1621434/000162843425006345/form10q.htm"
reporting_quarter: "Q3 2025"
```

### Processing
1. ✓ Detect hedging program: YES (found MD&A section with hedge tables)
2. ✓ Extract oil swap: Q4 2025 @ $71.22 strike, 555k BBL
3. ✓ Extract gas swap: Q4 2025 @ $3.64 strike, 11.04B BBtu
4. ✓ Extract fair value: +$45M MTM gain
5. ✓ Validate all data: PASSED
6. ✓ Check red flags: DECLINING_STRIKE_LADDER detected
7. ✓ Output 2 JSON records (one oil, one gas)

### Output
```json
[
  {
    "hedge_id": "BSM-OIL-Q4_2025-001",
    "company_ticker": "BSM",
    "commodity": "WTI_CRUDE_OIL",
    "total_volume": 555000,
    "weighted_avg_strike": 71.22,
    ...
    "risk_flags": [...]
  },
  {
    "hedge_id": "BSM-GAS-Q4_2025-002",
    "company_ticker": "BSM",
    "commodity": "NYMEX_NATURAL_GAS",
    "total_volume": 11040000,
    "weighted_avg_strike": 3.64,
    ...
  }
]
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-10-23 | Initial specification; generic company support; no hardcoding |

