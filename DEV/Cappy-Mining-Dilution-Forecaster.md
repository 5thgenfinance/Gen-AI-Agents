---
agent_name: Cappy
agent_full_name: Cappy - Capital Raise Yield Predictor
agent_acronym: CRYP
agent_type: RAG (Retrieval-Augmented Generation)
title: Cappy - Junior Mining Dilution Risk Forecasting Agent
subtitle: Model Specification v2.3
version: 2.3
status: Production
date_created: 2024-Q1
last_updated: 2025-10-25
author: Junior Mining Analytics Team

# Agent Identity
agent_metadata:
  name: Cappy
  full_name: Capital Raise Yield Predictor
  description: Forecasts dilution risk for junior mining capital raises
  model_type: RAG Agent
  purpose: Predict probability, magnitude, and timing of shareholder dilution

document_type: Technical Specification
content_type: AI Agent Model Specification

tags:
  - Cappy
  - RAG
  - Mining
  - Risk Forecasting
  - Dilution
  - Capital Raises
  - Junior Mining Companies

keywords:
  - dilution risk
  - capital raises
  - junior mining
  - composite risk scoring
  - RAG agent
  - forecasting model
  - financial distress

abstract: |
  Cappy is a RAG (Retrieval-Augmented Generation) agent that predicts capital raise 
  dilution risk for junior mining companies using empirical data from 2020–2025. 
  It outputs color-coded risk categories based on probability, magnitude, and timing 
  of expected dilution events across gold, silver, uranium, potash, copper, and 
  base metals sectors.

scope:
  commodities:
    - Gold
    - Silver
    - Uranium
    - Potash
    - Copper
    - Base Metals
  data_period: 2020-2025
  company_coverage: 2000+
  historical_raises: 5000+
  geographic_focus: North American junior miners

metadata:
  model_version: 2.1
  data_version: October 2025
  maintained_by: Junior Mining Analytics Team
  support_email: support@juniormininganalytics.com
  documentation_url: https://docs.juniormininganalytics.com/cappy-v2.1
  api_endpoint: https://api.juniormininganalytics.com/cappy/v2.1

compliance:
  uses_models: true
  includes_formulas: true
  requires_data_updates: true
  update_frequency: Weekly (Mondays 6:00 AM ET)
  training_period: Quarterly
---



# Junior Mining Dilution Risk Forecasting RAG Agent - Model Specification v2.2

## Overview
This RAG (Retrieval-Augmented Generation) agent predicts **capital raise dilution risk** for junior mining companies (gold, silver, uranium, potash, copper, and base metals) using empirical data from 2020–2025. The model outputs **color-coded risk categories** based on probability, magnitude, and timing of expected dilution events.

---

## Model Architecture

### Input Schema
```json
{
  "tickers": ["string"],
  "evaluation_date": "YYYY-MM-DD",
  "quarters_ahead": 6,
  "macro_scenario": "Bull | Neutral | Bear",
  "include_rationale": true
}
```

#### Example Input
```json
{
  "tickers": ["VZLA", "DV", "WRLG"],
  "evaluation_date": "2025-10-25",
  "quarters_ahead": 6,
  "macro_scenario": "Bull",
  "include_rationale": true
}
```

---

## Output Schema

### Color-Coded Risk Categories
The model classifies dilution risk into four tiers using **composite risk scoring**:

| Risk Level | Indicator | Criteria | Score Range |
|------------|-----------|----------|-------------|
| 🔴 **VERY HIGH RISK** | Red Bullet | Probability ≥35% + Dilution ≥12% + Timing ≤5Q | 0.55+ |
| 🟠 **HIGH RISK** | Orange Bullet | Probability ≥35% OR (Dilution ≥15% + Prob ≥30%) | 0.40-0.54 |
| 🟡 **MEDIUM RISK** | Yellow Bullet | Probability ≥25% OR Dilution ≥12% | 0.25-0.39 |
| 🟢 **LOW RISK** | Green Bullet | Probability <25% AND Dilution <12% | <0.25 |

### Output Format
```json
{
  "ticker": "string",
  "company": "string",
  "commodity": "string",
  "risk_category": "VERY HIGH RISK | HIGH RISK | MEDIUM RISK | LOW RISK",
  "risk_indicator": "🔴 | 🟠 | 🟡 | 🟢",
  "risk_score": 0.0,
  "probability_of_raise_next_6q": 0.0,
  "expected_raise_size_million_CAD": 0.0,
  "predicted_dilution_percent": 0.0,
  "timing_quarters": 0,
  "timing_description": "string",
  "market_cap_million_CAD": 0.0,
  "months_since_last_raise": 0,
  "cash_position": "Very Strong | Strong | Moderate | Weak",
  "rationale": "string"
}
```

#### Example Output
```json
{
  "ticker": "WRLG",
  "company": "West Red Lake Gold",
  "commodity": "Gold",
  "risk_category": "VERY HIGH RISK",
  "risk_indicator": "🔴",
  "risk_score": 1.134,
  "probability_of_raise_next_6q": 45.0,
  "expected_raise_size_million_CAD": 50.0,
  "predicted_dilution_percent": 18.0,
  "timing_quarters": 4,
  "timing_description": "Q3 2026 (post-commercial production ramp)",
  "market_cap_million_CAD": 300.0,
  "months_since_last_raise": 1,
  "cash_position": "Moderate",
  "rationale": "Production ramp-up at Madsen Mine. Working capital needs during ramp-up phase. Recent C$41M raise (Sep 2025) may not cover full optimization to steady-state production. Warrant exercises (C$120M potential) could reduce pressure but timing uncertain. Commercial production target Q1 2026 will reveal true cash burn rate."
}
```

### Social Media Post Output Format

For distribution to social media platforms and investor communications, Cappy can generate formatted posts with validation metadata:
```
{
"social_media_post": {
"ticker": "string",
"company_name": "string",
"primary_mineral": "string",
"risk_level": "string (VERY HIGH RISK | HIGH RISK | MEDIUM RISK | LOW RISK)",
"risk_indicator": "string (🔴 | 🟠 | 🟡 | 🟢)",
"expected_dilution_magnitude": "X-Y%",
"dilution_reason": "string (2-3 sentences)",
"formatted_post": "string (ready to post)"
},
"validation_checks": {
"evaluation_date": "YYYY-MM-DD",
"data_cutoff_date": "YYYY-MM-DD",
"company_status": {
"exists_as_of_eval_date": true | false,
"status_message": "string"
},
"data_completeness": {
"recent_financing_complete": true | false,
"financings_checked_period": "YYYY-MM-DD to YYYY-MM-DD",
"missing_raises_detected": false | "Financing announcement on YYYY-MM-DD not in database",
"last_updated": "YYYY-MM-DD"
},
"assumptions_validation": {
"macro_environment_current": true | false,
"commodity_prices_validated": true | false,
"strategic_investors_current": true | false,
"notes": "string"
},
"accuracy_confidence": {
"confidence_score": 0.0-1.0,
"confidence_level": "High | Medium | Low",
"primary_factors": ["factor1", "factor2", "factor3"],
"potential_blind_spots": ["issue1", "issue2"]
},
"validation_passed": true | false,
"validation_warnings": ["warning1", "warning2"],
"ready_to_post": true | false
}
}
```

#### Example Social Media Output

```
{
"social_media_post": {
"ticker": "WRLG",
"company_name": "West Red Lake Gold",
"primary_mineral": "Gold",
"risk_level": "VERY HIGH RISK",
"risk_indicator": "🔴",
"expected_dilution_magnitude": "15-20%",
"dilution_reason": "Madsen Mine ramping to commercial production in Q1 2026 will require significant working capital. Recent C$41M raise (Sept 2025) may not sustain full-scale operations. Expect additional financing within 12-18 months to fund optimization phase.",
"formatted_post": "🔴 DILUTION ALERT: $WRLG\n\nWest Red Lake Gold faces VERY HIGH dilution risk over the next 12-18 months.\n\nExpected dilution: 15-20%\nPrimary driver: Production ramp-up at Madsen Mine (Q1 2026 target)\n\nWorking capital needs during transition to steady-state operations likely to trigger capital raise Q2-Q3 2026.\n\n#JuniorMining #Gold #DilutionRisk #MiningFinance"
},
"validation_checks": {
"evaluation_date": "2025-11-07",
"data_cutoff_date": "2025-11-06",
"company_status": {
"exists_as_of_eval_date": true,
"status_message": "West Red Lake Gold confirmed active, trading on TSX-V as of Nov 2025"
},
"data_completeness": {
"recent_financing_complete": true,
"financings_checked_period": "2025-08-07 to 2025-11-07",
"missing_raises_detected": false,
"last_updated": "2025-11-06"
},
"assumptions_validation": {
"macro_environment_current": true,
"commodity_prices_validated": true,
"strategic_investors_current": true,
"notes": "Gold prices stable at ~$2,650/oz. No new major investor announcements. Madsen production timeline confirmed in latest MD&A."
},
"accuracy_confidence": {
"confidence_score": 0.88,
"confidence_level": "High",
"primary_factors": ["Cash runway analysis", "Production timeline", "Historical burn rates"],
"potential_blind_spots": ["Warrant exercise timing uncertain", "Permitting delays possible", "Commodity price swings"]
},
"validation_passed": true,
"validation_warnings": [],
"ready_to_post": true
}
}
```





---

## Risk Scoring Formula

### Composite Risk Score
The model calculates a **weighted composite score** combining probability, magnitude, and timing:

\[
\text{Risk Score} = \left(\frac{P_{raise}}{100}\right) \times \left(\frac{D_{expected}}{10}\right) \times \left[1 + \frac{2}{T_{quarters} + 1}\right]
\]

Where:
- \( P_{raise} \) = Probability of raise (0-100%)
- \( D_{expected} \) = Expected dilution percentage
- \( T_{quarters} \) = Number of quarters until most likely raise

**Interpretation:**
- **Higher score = Higher risk**
- Near-term raises weighted more heavily via timing multiplier
- Considers both likelihood AND impact

### Example Calculation
**West Red Lake Gold (WRLG):**
- Probability: 45%
- Expected Dilution: 18%
- Timing: 4 quarters

\[
\text{Risk Score} = \frac{45}{100} \times \frac{18}{10} \times \left[1 + \frac{2}{4+1}\right] = 0.45 \times 1.8 \times 1.4 = 1.134
\]

---

## Predictive Features & Weights

### Feature Extraction
The RAG retrieval module extracts the following features for each company:

| Feature Category | Specific Indicators | Weight | Data Source |
|------------------|---------------------|--------|-------------|
| **Financial Distress** | Cash runway <6 months | 0.25 | Financials, MD&A |
| | Working capital deficit | 0.15 | Balance sheet |
| | Burn rate acceleration | 0.10 | Cash flow statements |
| **Commodity Environment** | Metal price YoY change | 0.20 | Market data |
| | Sector sentiment (GDXJ) | 0.10 | Index performance |
| **Project Stage** | Development milestones | 0.10 | News, presentations |
| | PEA/PFS/FS advancement | 0.15 | Technical reports |
| **Strategic Backing** | Investor presence | 0.10 | Shareholder lists |
| **Market Capitalization** | Size tier | 0.15 | Market data |
| **Recent Financing** | Months since last raise | 0.20 | Press releases |
| **Operational Status** | Producer vs. explorer | 0.15 | Company reports |

### Predictive Model Logic

#### Step 1: Cash Runway Assessment
```python
if cash_runway_months < 3:
    probability_base = 0.85  # Very high
elif cash_runway_months < 6:
    probability_base = 0.65  # High
elif cash_runway_months < 12:
    probability_base = 0.35  # Moderate
else:
    probability_base = 0.15  # Low
```

#### Step 2: Macro Adjustment
```python
if macro_scenario == "Bull":
    probability_multiplier = 0.85  # Lower probability due to better access
    dilution_multiplier = 0.70    # Lower dilution due to premium pricing
elif macro_scenario == "Neutral":
    probability_multiplier = 1.0
    dilution_multiplier = 1.0
else:  # Bear
    probability_multiplier = 1.25  # Higher probability (distressed)
    dilution_multiplier = 1.40    # Higher dilution (discounted pricing)
```

#### Step 3: Project Stage Adjustment
```python
stage_factors = {
    "Producer (cash flow positive)": {"prob": 0.70, "dilution": 0.80},
    "Producer (ramp-up)": {"prob": 1.10, "dilution": 1.15},
    "Development (permitted)": {"prob": 1.05, "dilution": 0.95},
    "Advanced exploration": {"prob": 1.15, "dilution": 1.10},
    "Early exploration": {"prob": 1.30, "dilution": 1.25}
}
```

#### Step 4: Market Cap Efficiency
```python
if market_cap > 2000:  # Large cap
    dilution_efficiency = 0.75
elif market_cap > 500:  # Mid cap
    dilution_efficiency = 1.0
else:  # Small cap (<$500M)
    dilution_efficiency = 1.35
```

#### Step 5: Strategic Investor Factor
```python
if strategic_investor_present and strategic_stake > 10:
    probability_reduction = 0.15  # Lower prob (access to private capital)
    dilution_reduction = 0.10    # Better terms
else:
    probability_reduction = 0.0
    dilution_reduction = 0.0
```

---

## Validation & Backtesting

### Historical Accuracy (2023-2025 Validation Set)
| Metric | Performance |
|--------|-------------|
| **Prediction Accuracy (±1 quarter)** | 82% |
| **Dilution Magnitude Error** | ±8% MAPE |
| **False Positives (predicted raise, none occurred)** | 12% |
| **False Negatives (missed raise)** | 6% |

### Calibration Metrics
- **AUC-ROC Score**: 0.87 (excellent discrimination)
- **Brier Score**: 0.14 (well-calibrated probabilities)
- **Precision@Top20%**: 0.91 (high-risk predictions very reliable)

---

## Deployment Configuration

### API Endpoint Structure
```
POST /api/v2.1/dilution-risk-forecast
Content-Type: application/json

{
  "tickers": ["VZLA", "WRLG", "DV"],
  "evaluation_date": "2025-10-25",
  "macro_scenario": "Bull"
}
```

### Response Time SLA
- Single ticker: <2 seconds
- Batch (≤10 tickers): <8 seconds
- Batch (≤50 tickers): <30 seconds

### Update Frequency
- **Real-time triggers**: Capital raise announcements, quarterly financials
- **Scheduled updates**: Weekly (every Monday 6:00 AM ET)
- **Model retraining**: Quarterly with new financing data

### Social Media Output Configuration

When `include_social_format: true` is set in input:

```json
{
  "request_social_output": {
    "include_social_format": true,
    "validation_required": true,
    "validation_level": "strict",
    "platform_targets": ["LinkedIn", "Twitter", "email"]
  }
}
```

**Validation Requirements** (when `validation_required: true`):

- Company existence verified as of evaluation date
- Recent financing data completeness confirmed (last 90 days)
- Commodity price data current (within 1 trading day)
- Macro scenario assumptions validated
- Confidence score ≥0.75 required for `ready_to_post = true`

**Platform-Specific Formatting**:

- **LinkedIn**: Full formatted post with hashtags and metrics
- **Twitter**: Condensed version (280 character limit)
- **Email**: HTML with company logo and risk indicator colors

---

## Use Cases

### For Investors
- **Pre-investment screening**: Avoid companies with imminent high dilution risk
- **Portfolio monitoring**: Track existing holdings for dilution alerts
- **Entry timing**: Wait for post-financing entry at lower share counts

### For Companies (IR/CFO)
- **Optimal financing timing**: Identify bull market windows for premium pricing
- **Dilution minimization**: Plan raises when risk score is lowest
- **Strategic investor targeting**: Understand when backing is most valuable

### For Analysts
- **DCF/NAV modeling**: Incorporate dilution forecasts into share count projections
- **Target price adjustments**: Factor expected dilution into valuations
- **Relative ranking**: Compare dilution risk across peer groups

---

## Limitations & Disclaimures

### Known Limitations
1. **Data availability**: Private placements may have delayed disclosure
2. **Strategic events**: M&A, streaming deals can rapidly change financing needs
3. **Commodity volatility**: Rapid price changes can shift economics quickly
4. **Jurisdiction risk**: Permitting delays, political events hard to predict
5. **Management decisions**: Capital allocation choices are discretionary

### Model Assumptions
- Historical patterns (2020-2025) remain predictive
- Macro scenario classifications are accurate
- Company-reported cash positions are reliable
- Strategic investor relationships are stable

### Recommended Usage
- **Use as screening tool**, not sole decision factor
- **Combine with fundamental analysis** and management assessment
- **Update regularly** as new information emerges
- **Validate predictions** against actual raises post-event

---

## Version History

### v2.2
- Added social media post generation with integrated validation workflow

### v2.1 (October 2025)
- Added color-coded risk categories (🔴🟠🟡🟢)
- Implemented composite risk scoring formula
- Enhanced timing proximity weighting
- Added bull/neutral/bear macro scenarios
- Improved strategic investor detection

### v2.0 (June 2025)
- Initial production release
- 30+ predictive factors
- Historical database (2020-2025)
- Backtesting framework

### v1.0 (January 2025)
- Prototype version
- Basic probability model
- Limited to gold/silver companies

---

## Pre-Publication Validation Workflow

### Purpose

Before publishing social media posts or investor communications from Cappy forecasts, a comprehensive validation workflow ensures all output reflects current, accurate, and complete information. This workflow prevents distribution of outdated or incomplete analyses.

### Step 1: Company Existence Verification

- ✅ Confirm company still exists and trades actively as of evaluation date
- ✅ Verify ticker symbol has not been delisted or reversed-merged
- ✅ Check company website and SEC/CSE filings for bankruptcy, restructuring, or strategic changes
- ✅ Validate company status in Bloomberg, Reuters, and company official sources


### Step 2: Recent Financing Completeness (Last 90 Days)

- ✅ Cross-reference all press releases for equity raises, private placements, debt conversions
- ✅ Check stock exchange filings (Form 8-K for US, material change reports for Canada)
- ✅ Verify no announced raises are missing from the capital raise database
- ✅ Flag if any financing gap exists in the checked period; pause publication until resolved
- ✅ Confirm database was refreshed within 24 hours of evaluation date


### Step 3: Data Currency \& Accuracy

- ✅ Confirm all financial data (cash position, burn rate) from latest quarterly filings
- ✅ Validate commodity prices from last trading day
- ✅ Verify production timelines against latest MD\&A or investor presentations
- ✅ Check for strategic investor changes in recent shareholder disclosures (Schedule 13D/13G, insider trades)


### Step 4: Scenario Assumptions Validation

- ✅ Confirm macro environment assessment (Bull/Neutral/Bear) remains accurate
- ✅ Review metal price trends for material changes since model run
- ✅ Assess if any permitting, geological, or operational setbacks have occurred
- ✅ Verify management team stability (no unexpected C-suite departures)
- ✅ Check for any news items contradicting the forecast


### Step 5: Output Sanity Checks

- ✅ Does predicted dilution % align with company size and historical raise patterns?
- ✅ Is timing (12-18 month window) consistent with cash runway analysis?
- ✅ Does the rationale make intuitive sense given the company's stage and metrics?
- ✅ Are there any recent news items contradicting the forecast?
- ✅ Is confidence score ≥0.75 for publication?


### Step 6: Confidence \& Risk Assessment

- ✅ Confidence score should be ≥0.75 for publication
- ✅ If confidence <0.75, note primary uncertainties and consider withholding post
- ✅ Verify no material blind spots (e.g., pending permit decisions, M\&A rumors, warrant exercises)
- ✅ Flag any conflicts of interest or data quality issues


### Pre-Publication Data Gathering

**Before posting, human reviewers should pull:**

1. Latest investor presentation or earnings call transcript
2. Recent news on company's project status (last 30 days)
3. Current market cap and share price (compare to model inputs for accuracy)
4. Recent analyst reports or news alerts published in last 30 days
5. Warrant exercise tracker (if material amounts outstanding)
6. Strategic investor announcements or share acquisitions

### Publication Approval Checklist

| Item | Status | Notes |
| :-- | :-- | :-- |
| Company exists and trades as of eval date | ✅ |  |
| Recent financing data complete (last 90 days) | ✅ |  |
| Latest quarterly financials incorporated | ✅ |  |
| Commodity prices current | ✅ |  |
| Production timelines verified | ✅ |  |
| Macro scenario still valid | ✅ |  |
| Confidence score ≥0.75 | ✅ |  |
| No blind spots detected | ✅ |  |
| All warnings reviewed and acceptable | ✅ |  |
| Ready for publication | ✅ |  |

### Handling Failed Validation

If validation fails at any step:

1. **Pause publication** of the social media post
2. Return to data quality checks in step 2-3
3. Refresh capital raise database if needed
4. Update macro scenario if markets have shifted materially
5. Rerun forecast with current inputs
6. Retry validation workflow
7. Document reason for delay for audit trail

### Post-Publication Monitoring

After publishing:

- Monitor for company news that contradicts the forecast
- Track actual vs. predicted raise timing and size
- Document any model adjustments needed based on post-hoc analysis
- Update model calibration if systematic bias detected


---

## Contact & Support

**Model Maintainer**: Junior Mining Analytics Team  
**Last Updated**: October 25, 2025  
**Model Version**: 2.1  
**Data Coverage**: 2,000+ mining companies, 5,000+ historical raises  

For questions, calibration requests, or custom implementations:
- Email: support@juniormininganalytics.com
- Documentation: https://docs.juniormininganalytics.com/dilution-rag-v2.1
- API Access: https://api.juniormininganalytics.com/v2.1

---

© 2025 Junior Mining Dilution Risk Assessment Framework
Licensed under Apache 2.0 for research and commercial use


---

## Recent Financing Completeness Check

### Purpose

To ensure the model forecasts are based on complete and up-to-date financing information, the system incorporates a mandatory check that all equity raises, private placements, and financings executed in the last **3 calendar months** prior to the evaluation date are accounted for in the capital raise database.

### Implementation Details

- During data ingestion, the agent cross-references publicly disclosed financings (press releases, filings, stock exchange announcements) against the internal capital raise dataset.
- Any raise occurring within minus 3 calendar months of the evaluation date that is not recorded triggers a warning flag.
- The agent will temporarily **pause the dilution risk computation** for the affected ticker(s) until the database is updated.
- An output field `recent_financing_complete` is included in the response, returning `true` if completeness is met, otherwise `false`.
- Incomplete cases include a message outlining the missing financing period and suggest manual intervention or data refresh.

### Output Schema Update

Add to the existing JSON output:
```json
"recent_financing_complete": true | false,
"completeness_message": "string"  // Present if completeness is false
```

### Example Output for Incomplete Case
```json
{
  "ticker": "XYZ",
  "risk_category": "Unknown",
  "recent_financing_complete": false,
  "completeness_message": "Financing data for the period from 2025-07-01 to 2025-10-01 is missing. Please update capital raise database."
}
```

### Testing Note

Recommended to add test scenarios where:
- A financing announcement is published within last 3 months but **not present** in the capital raise database.
- The system outputs `recent_financing_complete = false` and correctly pauses risk scoring for those tickers.

Manual or automated database updates are required to resolve completeness before actionable forecasts can be produced.
