---
agent_name: Cappy
agent_full_name: Cappy - Capital Raise Yield Predictor
agent_acronym: CRYP
agent_type: RAG (Retrieval-Augmented Generation)
title: Cappy - Junior Mining Dilution Risk Forecasting Agent
subtitle: Model Specification v2.51
version: 2.51
status: Production
date_created: 2024-Q1
last_updated: 2025-10-25
author: Robert Maxwell
deterministic: true # top-p and top-k parameters are not used
frequency_penalty = 0

# Agent Identity
agent_metadata:
  name: Cappy
  full_name: Capital Raise Yield Predictor
  description: Forecasts dilution risk for junior mining capital raises using certified financials, interim activity, and capital raise signals
  model_type: RAG Agent with Hierarchical Data Sourcing
  purpose: Predict probability, magnitude, and timing of shareholder dilution

document_type: Technical Specification with Data Sourcing Workflow
content_type: AI Agent Model Specification + Data Collection Framework

tags:
  - Cappy
  - RAG
  - Mining
  - Risk Forecasting
  - Dilution
  - Capital Raises
  - Junior Mining Companies
  - Data Sourcing
  - SEC Filings
  - SEDAR+
  - Token Efficiency

keywords:
  - dilution risk
  - capital raises
  - junior mining
  - composite risk scoring
  - RAG agent
  - forecasting model
  - financial distress
  - certified financials
  - earnings call analysis
  - warrant tracking

abstract: |
  Cappy v2.51 is an enhanced RAG (Retrieval-Augmented Generation) agent that predicts capital raise 
  dilution risk for junior mining companies using a hierarchical data sourcing framework prioritizing:
  (1) Certified financials (10-Q/10-K, SEDAR+ MD&A), (2) Interim financial activity (8-K, Material 
  Facts, insider transactions), and (3) Forward-looking capital plans (earnings call transcripts, 
  management guidance). This version reduces token usage by 40-50% while maintaining forecast fidelity.

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
  geographic_focus: North American junior miners (U.S. and Canada)
  listing_exchanges:
    - NASDAQ
    - NYSE
    - TSX
    - TSX-V
    - OTC Markets

output_generation:
  includes_4_panel_widget: true
  widget_class: CappyDilutionDashboard
  widget_output_formats:
    - standalone_html
    - embedded_json
    - social_media_post

metadata:
  model_version: 2.51
  data_version: November 2025
  maintained_by: Fifth Gen Finance
  support_email: Robert.Maxwell@5thgenfinance.com
  documentation_url: https://www.5thgenfinance.com


compliance:
  uses_models: true
  includes_formulas: true
  requires_data_updates: true
  update_frequency: Weekly (Mondays 6:00 AM ET)
  training_period: Quarterly
---

# SECTION 1: DATA SOURCING STRATEGY (NEW IN v2.5)

## Overview

Cappy v2.5 implements a **hierarchical, token-efficient data sourcing framework** that prioritizes high-signal official sources before lower-signal general web search. This reduces token waste by 40-50% while maintaining analytical rigor.

### Sourcing Philosophy

**Principle 1: Official First**  
Certified financial documents (SEC, SEDAR+, regulatory filings) are authoritative and should be retrieved before analyst commentary or general news.

**Principle 2: Recency Matters**  
Recent interim activity (8-K, Material Facts, insider transactions) reveals current capital pressure signals. Search for activity within last 90 days first.

**Principle 3: Forward Guidance is Predictive**  
Earnings call transcripts and MD&A forward guidance are the best predictor of future capital needs. Analyze last 3-4 quarters for trends.

**Principle 4: Token Efficiency**  
Stop searching once sufficient data is found. Tier 1 + partial Tier 2 typically yields 90%+ of required information.

---

## TIER 1: OFFICIAL CERTIFIED SOURCES (HIGHEST PRIORITY)

### 1.1 SEC Filings (U.S.-listed companies)

**10-Q (Quarterly Report)**
- **Filing Frequency:** 40-45 days after quarter-end
- **Audit Status:** Reviewed by external auditor
- **Key Dilution Data:**
  - Cash position and liquidity schedule
  - Debt balance, interest rates, conversion terms
  - Diluted EPS calculation (includes all dilutive instruments)
  - MD&A section: Management discussion of capital needs, financing plans
  - Capitalization table in equity section: shares outstanding, options, warrants
  - Recent equity offerings or debt conversions
- **Search Query:** `"[ticker] 10-Q site:sec.gov"` or `"[ticker] quarterly report form 10-Q"`
- **Token Cost:** 1-2K tokens per 10-Q
- **Priority:** CRITICAL - Always retrieve most recent quarter first

**10-K (Annual Report)**
- **Filing Frequency:** 60 days after fiscal year-end
- **Audit Status:** Fully audited
- **Key Dilution Data:**
  - Full-year capitalization table with warrant and option details
  - 5-year historical share count trends
  - Item 5 (Market for Registrant's Common Equity): insider holdings
  - Item 10 (Directors, Executive Officers): management background
  - Stock option and warrant plans: total shares reserved, exercise prices, expiration dates
  - Related-party transactions
  - Risk factors: going concern issues, capital needs
- **Search Query:** `"[ticker] 10-K site:sec.gov"` or `"[ticker] annual report form 10-K"`
- **Token Cost:** 2-3K tokens per 10-K
- **Priority:** HIGH - Retrieve if 10-Q lacks historical context

**8-K (Current Report / Material Event)**
- **Filing Frequency:** Within 4 business days of event
- **Audit Status:** Non-audited but regulated
- **Key Dilution Data:**
  - Capital raises: equity offerings, private placements, registered direct offerings
  - Debt issuances: convertible notes, debentures, warrants
  - Material acquisitions or partnerships affecting share count
  - Executive departures or changes in control
  - Warrant exercises (Item 8.01: Other Events)
  - Bankruptcy, restructuring, or going-concern issues
- **Search Query:** `"[ticker] 8-K capital site:sec.gov"` or `"[ticker] 8-K convertible"` or `"[ticker] material event"`
- **Token Cost:** 0.5-1K per 8-K (usually short)
- **Priority:** CRITICAL - Search within last 90 days for interim capital activity

**424B5 / 424B2 (Prospectus Supplement)**
- **Filing Frequency:** When filed (during capital raise announcement)
- **Audit Status:** Official prospectus for new offerings
- **Key Dilution Data:**
  - Exact conversion price for convertible securities
  - Capped-call hedging terms (prevents dilution up to specific price)
  - Number of warrants issued, exercise price, expiration
  - Use of proceeds
  - Risk factors specific to the offering
- **Search Query:** `"[ticker] prospectus site:sec.gov 424B"` or `"[ticker] convertible prospectus"`
- **Token Cost:** 1-2K per prospectus
- **Priority:** CRITICAL for recent capital raises - Contains exact terms

**DEF 14A (Proxy Statement / Annual Proxy)**
- **Filing Frequency:** ~30-45 days before annual shareholder meeting (usually spring)
- **Audit Status:** Official proxy
- **Key Dilution Data:**
  - Section 4: Executive compensation, including stock option grants
  - Item 5: Directors and Officers, with background and stock holdings
  - Stock option and equity incentive plans: shares reserved, current grants
  - Say-on-Pay votes (indicates shareholder sentiment on dilution)
  - Related-party transactions
- **Search Query:** `"[ticker] DEF 14A site:sec.gov"` or `"[ticker] proxy statement"`
- **Token Cost:** 2-3K per proxy
- **Priority:** MEDIUM-HIGH - Run annually for compensation/option context

**Form 4 (Insider Transaction)**
- **Filing Frequency:** Within 2 business days of transaction
- **Audit Status:** Real-time regulatory filing
- **Key Dilution Data:**
  - Insider stock purchases (confidence signal: LOW dilution pressure expected)
  - Insider stock sales (stress signal: HIGH dilution pressure suspected)
  - Option exercises and vesting
  - Magnitude and pricing of transactions
  - Timing trends (acceleration of insider selling = warning signal)
- **Search Query:** `"[ticker] form 4 site:sec.gov"` or `"[ticker] insider transaction"`
- **Token Cost:** 0.5-1K total (usually high volume of filings)
- **Priority:** HIGH - Track last 3-6 months for insider behavior patterns

---

### 1.2 SEDAR+ (Canada-listed companies: TSX, TSX-V)

**Quarterly MD&A (Management Discussion & Analysis)**
- **Filing Frequency:** Within 90 days of quarter-end
- **Audit Status:** Audited (MD&A section)
- **Key Dilution Data:**
  - Cash position and burn rate
  - Capital expenditure plan and timing
  - Cash used in operations (burn rate)
  - Debt balances and terms
  - Financing activities: new equity, debt, warrant/option exercises
  - Management's assessment of capital sufficiency
  - Related-party transactions and related-party financing
- **Search Query:** `"[ticker] MD&A site:sedarplus.ca"` or `"[ticker] management discussion"`
- **Token Cost:** 1-2K per MD&A
- **Priority:** CRITICAL - Always retrieve most recent quarter first

**Annual Financial Statements + MD&A**
- **Filing Frequency:** Within 120 days of fiscal year-end
- **Audit Status:** Fully audited
- **Key Dilution Data:**
  - Full capitalization table in equity section of balance sheet
  - Warrant and option schedules with exercise prices and expiration dates
  - 5-year historical share count
  - Notes to financial statements: related-party financing terms
  - Stock-based compensation (warrants, options granted this year)
  - Any going-concern notes from auditor (flag for capital stress)
- **Search Query:** `"[ticker] annual financial statements site:sedarplus.ca"` or `"[ticker] audited financials"`
- **Token Cost:** 2-3K per annual filing
- **Priority:** HIGH - Retrieve if quarterly MD&A lacks historical context

**Material Change Reports (MCR)**
- **Filing Frequency:** Within 5 days of material event
- **Audit Status:** Official regulatory filing
- **Key Dilution Data:**
  - Capital raises: equity offerings, private placements, registered direct offerings
  - Debt issuances: convertible notes, debentures
  - Material partnerships or strategic investments
  - Warrant or option exercises
  - Acquisitions affecting share count
  - Going-concern or financial distress announcements
- **Search Query:** `"[ticker] material change site:sedarplus.ca"` or `"[ticker] MCR"`
- **Token Cost:** 0.5-1K per MCR (usually short)
- **Priority:** CRITICAL - Search within last 90 days for interim capital activity

**Prospectus / Offering Circular**
- **Filing Frequency:** When filed (during capital raise announcement)
- **Audit Status:** Official prospectus
- **Key Dilution Data:**
  - Exact conversion price for convertible securities
  - Warrant exercise price and expiration
  - Use of proceeds
  - Management discussion of capital plan
  - Risk factors specific to financing
- **Search Query:** `"[ticker] prospectus site:sedarplus.ca"` or `"[ticker] offering circular"`
- **Token Cost:** 1-2K per prospectus
- **Priority:** CRITICAL for recent capital raises

**NI 43-101 Technical Report (Mining)**
- **Filing Frequency:** As required for material projects (typically annual or major updates)
- **Audit Status:** Certified by independent QP (Qualified Person)
- **Key Dilution Data:**
  - Capital expenditure requirements for development/production
  - Project timeline and production ramp schedule (drives future capital needs)
  - Assumptions on commodity prices, operating costs
  - Resource estimates and recovery assumptions
  - Technical and regulatory risks affecting timeline
- **Search Query:** `"[company name] NI 43-101"` or `"[ticker] NI 43-101 site:sedarplus.ca"`
- **Token Cost:** 2-3K per report (long technical document)
- **Priority:** CRITICAL FOR MINING - Supersedes all other sources for capex/timeline data

**SEDI (System for Electronic Disclosure by Insiders)**
- **Filing Frequency:** Real-time insider transactions
- **Audit Status:** Real-time regulatory filing
- **Key Dilution Data:**
  - Insider stock purchases (confidence signal)
  - Insider stock sales (stress signal)
  - Option exercises and vesting
  - Transaction timing and magnitude
  - Beneficial ownership changes
- **Search Query:** `"[ticker] SEDI insider trading site:sedi.ca"`
- **Token Cost:** 0.5-1K total (usually high volume)
- **Priority:** HIGH - Track last 3-6 months for insider behavior patterns

---

## TIER 2: HIGH-QUALITY ANALYST & SECONDARY SOURCES (USE IF TIER 1 GAPS)

### 2.1 Earnings Call Transcripts (Last 3-4 Quarters)

**Why This Is Critical for Capital Plans:**
- Executives telegraph capital needs and financing plans 2-6 quarters ahead
- CFO typically discusses runway, burn rate, and next capital raise timing explicitly
- Q&A reveals investor concerns about dilution and capital structure

**Key Phrases to Listen For:**

| Management Quote | Signal | Dilution Implication |
|------------------|--------|----------------------|
| "We have sufficient capital through Q4 2025" | Specific runway disclosed | Capital need by Q1 2026; dilution likely ~12-18 months |
| "We are evaluating strategic partnerships" | Vague; capital partner discussed | 40-60% chance of dilutive financing or partnership terms |
| "We are well-capitalized for our growth" | Generic statement | Usually means NO near-term capital need; lower dilution risk <18m |
| "We completed $X financing at $Y/share" | Already closed | Measure dilution impact immediately; model future rounds |
| "We are in discussions with lenders" | Debt being pursued | Might avoid equity dilution; but watch for conversion clauses |
| "Convertible may be optimal for us" | Forward guidance | Higher leverage + conversion risk; model scenario |
| "We have $X cash and $Y quarterly burn" | Specific metrics | Calculate runway: Cash / Monthly Burn = months to capital need |

**How to Extract Data:**

1. Search: `"[ticker] Q3 2025 earnings call transcript"` (Seeking Alpha, company IR, TradingView, FactSet)
2. Download or read transcript in full
3. Search within transcript for: "capital," "financing," "raise," "convertible," "cash," "runway," "burn"
4. Extract CEO/CFO commentary in Forward Guidance section
5. Review Q&A for investor questions on capital structure

**Example: Encore Energy Q2 2025 Earnings Call Analysis**
- Likely statement: "The $115M convertible financing announced in August provides us runway through 2026"
- Implication: No capital need expected until Q4 2026; dilution risk LOW through mid-2026
- CFO would have mentioned capex ramp and cash burn to validate timing

**Search Query:** `"[ticker] earnings call transcript Q3 2025"` (Seeking Alpha, company IR)  
**Token Cost:** 2-3K per transcript (long document, but search-optimized)  
**Priority:** CRITICAL - Do NOT skip; reveals capital plans

---

### 2.2 Management Guidance in MD&A (Forward-Looking Section)

**What to Extract from MD&A Forward Guidance:**

- "We expect to spend $X capex in the next 12 months"
- "Our current cash position is $Y; quarterly run rate is -$Z"
- "We anticipate capital requirements of $X by Q[date]"
- "We have committed financing of $X for [project/development]"
- "We may need to raise capital if [condition]"

**Capital Need Calculation (Key Formula):**
```
(Projected Capex - Current Cash + Accumulated Burn) / Time Horizon = Monthly Capital Need
```

**Example for Encore Energy:**
- Current cash: $26.9M (Q2 2025)
- Q2 2025 burn: ~$8.75M (half of annualized ~$35M)
- Projected capex next 12m: ~$40-50M (Alta Mesa ramp, Upper Spring Creek)
- Committed financing: $115M convertible (August 2025)
- **Runway: $115M / ~$9M/month = ~12 months = Late 2026 capital need**

**Search Query:** Extract from 10-Q/MD&A directly (already retrieved in Tier 1)  
**Token Cost:** 0 (already in 10-Q; no new search needed)  
**Priority:** HIGH - Calculate immediately after retrieving 10-Q

---

### 2.3 Company Investor Relations Page & Press Releases

**What to Find:**

1. **Recent Press Releases (last 6 months)**
   - Capital raise announcements (exact terms, close date)
   - Financing completion announcements
   - Production updates (may indicate capex needs)
   - Partnership/strategic investment announcements

2. **Investor Presentation / Fact Sheet**
   - High-level capital structure
   - Near-term capex and funding plan
   - Production timeline and milestones
   - Key metrics: cash, burn rate, capex plans

3. **Email Alerts or News Archive**
   - Chronological view of all company news
   - Quickly identify material events

**Search Query:** `"[ticker] site:[company domain] press release"` or direct navigation to IR page  
**Token Cost:** 0.5-1K (usually concise announcements)  
**Priority:** MEDIUM - Good for dates and confirmation; less detail than official filings

---

### 2.4 Reputable Financial Analyst Sources (Use Sparingly)

**Approved Tier 2 Sources (Only If Tier 1 Gaps):**

- **Crux Investor:** Mining-specific analysis; uranium, gold focus
- **Seeking Alpha:** Analyst reports; use for validation only (watch for bias)
- **Mining.com:** Industry news and financing tracker
- **S&P Capital IQ / FactSet:** Structured financial data (if subscribed)

**What NOT to Use (Skip Entirely):**
- Reddit, Stocktwits, retail message boards
- Unverified Twitter/X commentary
- Competitor "short theses" (high bias)
- Articles >6 months old (mining data changes rapidly)

**Search Query:** `"[ticker] analyst forecast 2025"` or `"[company] capital raise analysis"` (Crux, Mining.com)  
**Token Cost:** 1-2K per source (set limit: max 2-3 analyst sources)  
**Priority:** MEDIUM - Use only to fill specific gaps or confirm findings from Tier 1

---

## TIER 3: GENERAL WEB SEARCH (LAST RESORT ONLY)

**Use Case:** Fill gaps if Tier 1 + Tier 2 insufficient (rare)

**Search Strategy:**
- Use general search: `"[company name] dilution" or "[ticker] capital raise 2025"`
- Set token limit: Max 1 query, max 1 result
- Accept lower quality; use only for data points not available in Tier 1/2

**Token Cost:** 1-2K (if used)  
**Priority:** LOW - Avoid if possible

---

# SECTION 2: INTEGRATED SOURCING WORKFLOW (NEW IN v2.5)

## Workflow: Complete Dilution Analysis in 3 Phases

### PHASE 1: BASELINE CERTIFIED FINANCIALS (Week 1)
**Objective:** Get core financial snapshot + capitalization structure  
**Estimated Time:** 2-3 hours  
**Token Budget:** 3-5K

**Steps:**
1. Retrieve **most recent 10-Q (U.S.) or quarterly MD&A (Canada)**
   - Extract: Cash, burn rate, debt balance, diluted share count
   - Flag: Any capital raises mentioned in MD&A

2. Retrieve **10-K (U.S.) or annual financials (Canada)** if historical context needed
   - Extract: Warrant/option schedules, 5-year share count trends, insider holdings

3. Search for **8-K or Material Change Reports in last 90 days**
   - Extract: Recent capital events, conversions, warrant exercises
   - Flag: Any announced but unclosed capital raises

**Output at End of Phase 1:**
- Current cash position ✓
- Quarterly burn rate ✓
- Debt structure and conversion terms ✓
- Current diluted share count ✓
- Recent capital activity ✓

---

### PHASE 2: FORWARD-LOOKING CAPITAL PLANS (Week 2)
**Objective:** Understand management's capital plans and runway  
**Estimated Time:** 3-4 hours  
**Token Budget:** 5-8K

**Steps:**
4. Extract **MD&A Forward Guidance** (from already-retrieved 10-Q/MD&A)
   - Calculate: Runway = Cash / Monthly Burn Rate
   - Identify: Capex needs and timing

5. Retrieve **Last 3-4 earnings call transcripts** (go back 12 months)
   - Listen for capital runway, financing plans, management guidance on raises
   - Track how guidance has evolved over quarters

6. Retrieve **Recent press releases** (company IR site, last 6 months)
   - Confirm close dates for announced capital raises
   - Note any uncommitted raises

**Output at End of Phase 2:**
- Estimated cash runway (months) ✓
- Expected next capital need timing ✓
- Management's stated capital plans ✓
- Trend in capital guidance (improving/worsening?) ✓

---

### PHASE 3: STRUCTURAL & VALIDATION (Week 3)
**Objective:** Validate capitalization structure, insider signals, peer context  
**Estimated Time:** 2-3 hours  
**Token Budget:** 3-5K

**Steps:**
7. Extract **capitalization table data** from latest 10-K/annual financials
   - Warrant schedule (exercise prices, expirations, exercises in period)
   - Option grants and exercises (dilution in last quarters)
   - Fully diluted share count assumptions

8. Retrieve **insider transaction activity** (FORM 4 or SEDI, last 6 months)
   - Map insider buys (confidence) vs. sells (stress)
   - Look for acceleration patterns

9. *Optional:* Peer financing comparisons (1-2 similar-stage competitors from Crux/Mining.com)
   - Context: Are other uranium plays raising capital dilutively? (sector-wide pressure?)

10. Validate **macro context:** Commodity prices, market sentiment, sector trends

**Output at End of Phase 3:**
- Diluted capitalization structure validated ✓
- Insider transaction signals (buy/sell pattern) ✓
- Peer financing context (if helpful) ✓
- Macro scenario assessment (Bull/Neutral/Bear) ✓

---


## Total Research Effort

| Phase | Duration | Token Cost | Output |
|-------|----------|------------|--------|
| Phase 1: Certified Financials | 2-3h | 3-5K | Cash, burn, capital structure |
| Phase 2: Capital Plans | 3-4h | 5-8K | Runway, timing, management plans |
| Phase 3: Validation | 2-3h | 3-5K | Capitalization validated, insider signals, macro |
| **TOTAL** | **7-10h** | **11-18K tokens** | **Complete dilution analysis** |

**Comparison to Old Method:**
- Old (v2.4): ~7 hours, 40+ sources, 60-80K tokens
- New (v2.5): ~7-10 hours, ~15 sources, 11-18K tokens
- **Token savings: 65-75%**

---

# SECTION 3: SOURCING RULES & DECISION TREES

## Rule 1: Batch Related Metrics Into Single Searches

**Before:** 3 separate searches
- Search 1: `"[ticker] cash position"`
- Search 2: `"[ticker] debt balance"`
- Search 3: `"[ticker] capital needs"`

**After:** 1 batched search
- Search 1: `"[ticker] 10-Q cash debt capital"`

**Token Savings:** 50-60%

---

## Rule 2: Stop Searching Once Tier 1 Yields Key Metrics

**If Tier 1 (SEC/SEDAR+) contains:**
- ✓ Current cash
- ✓ Burn rate
- ✓ Debt structure
- ✓ Diluted share count
- ✓ Recent capital raises

**Then:** Skip Tier 2 analyst sources; proceed to earnings call transcripts only.

**Decision Tree:**
```
Does 10-Q/MD&A contain capital runway estimate? 
  → YES: Skip analyst commentary; go to earnings call
  → NO: Retrieve 1 analyst source (Crux/Mining.com) for capex context
```

---

## Rule 3: Cache Company Data Within Session

**If you've already retrieved [ticker]'s 10-Q in this session:**
- Don't re-search the 10-Q
- Reference previously retrieved data

**Example Violation:** 
- Hour 1: Search `"[ticker] 10-Q cash"`
- Hour 2: Search `"[ticker] 10-Q burn rate"` ← WRONG (already have 10-Q)
- Hour 2 (correct): Search 10-Q data already retrieved

---

## Rule 4: Earnings Call Transcripts = High Priority for Capital Plans

**Mandate:** Always retrieve last 3-4 quarterly earnings calls (unless company doesn't hold calls)

**Why:** 
- Executives explicitly discuss capital runway and next financing
- Q&A reveals investor concerns about dilution
- Management guidance changes between quarters (signals confidence or stress)

**Search Order:**
1. Most recent quarter first
2. Go back 2-3 more quarters to see trend

**Stop Condition:** Once management guidance on capital needs is clear across 3+ quarters

---

## Rule 5: Insider Transaction Data = Dilution Pressure Indicator

**Monitor:** FORM 4 (U.S.) or SEDI (Canada) for last 3-6 months

**Interpretation:**

| Pattern | Signal | Dilution Risk |
|---------|--------|---------------|
| Net insider BUYS (no sells) | Management confident | LOW dilution risk (for 12m) |
| Mix of buys and sells (normal) | Active trading | NORMAL |
| Sudden acceleration of insider SELLS | Stress signal | HIGH dilution risk imminent |
| Large insider SELL + capital raise announcement | Double confirmation | Very HIGH risk; dilution likely |

**Example:** If CEO & CFO both sell 100K shares each in Month 1, then company announces $50M capital raise in Month 2 at 20% discount, this confirms DILUTION.

## SECTION 3.1: INTERACTIVE DASHBOARD WIDGET GENERATION

After Cappy v2.5 completes composite risk scoring (Risk Score Calculation), 
generate an interactive Plotly dashboard widget for visual risk communication.

### Implementation: CappyDilutionDashboard Class (Inline)

```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import pandas as pd

class CappyDilutionDashboard:
    """
    Cappy v2.5 - Fixed version for reliable 4-panel rendering
    """

    def __init__(self, company_name, ticker, evaluation_date, risk_score, risk_category):
        self.company_name = company_name
        self.ticker = ticker
        self.evaluation_date = evaluation_date
        self.risk_score = risk_score
        self.risk_category = risk_category

    def create_dashboard(self, financial_data, timeline_data, capital_structure):
        """Generate 4-panel dashboard with explicit positioning"""

        # Create figure with subplots using explicit positioning
        fig = make_subplots(
            rows=2, cols=2,
            subplot_titles=(
                "Cash Position vs. Capex Need",
                "Project Timeline & Capital Requirements",
                "Risk Score Gauge",
                "Capital Structure Dilution"
            ),
            specs=[
                [{"type": "bar", "rowspan": 1, "colspan": 1}, 
                 {"type": "scatter", "rowspan": 1, "colspan": 1}],
                [{"type": "indicator", "rowspan": 1, "colspan": 1}, 
                 {"type": "pie", "rowspan": 1, "colspan": 1}]
            ],
            vertical_spacing=0.15,
            horizontal_spacing=0.12,
            row_heights=[0.5, 0.5],
            column_widths=[0.5, 0.5]
        )

        # PANEL 1: Cash vs Capex (Top-left)
        categories = ['Current Cash', 'Monthly Burn\n(x10 mo.)', 'PEA + Trial\nCapex', 'Shortfall']
        values = [
            financial_data['cash'],
            financial_data['monthly_burn'] * 10,
            financial_data['capex_need'],
            financial_data['shortfall']
        ]
        colors = ['#2ecc71', '#e74c3c', '#f39c12', '#c0392b']

        fig.add_trace(
            go.Bar(
                x=values,
                y=categories,
                orientation='h',
                marker=dict(color=colors, line=dict(width=0)),
                text=[f"${v:.1f}M" for v in values],
                textposition='auto',
                textfont=dict(size=12, color='white'),
                hovertemplate='%{y}: $%{x:.1f}M<extra></extra>',
                showlegend=False
            ),
            row=1, col=1
        )

        # PANEL 2: Timeline (Top-right)
        timeline_df = pd.DataFrame(timeline_data)
        quarter_map = {'Q4 2025': 0, 'Q1 2026': 1, 'Q2-Q3 2026': 2.5, 'H2 2026': 3.5}
        timeline_df['quarter_num'] = timeline_df['quarter'].map(quarter_map)

        # Add bars for each milestone
        milestone_colors = {'PEA Complete': '#5DADE2', 'Trial Mining Starts': '#E74C3C', 
                           'Bulk Sample Phase': '#27AE60', 'PFS Development': '#5499C7'}

        for idx, row in timeline_df.iterrows():
            fig.add_trace(
                go.Bar(
                    x=[row['quarter']],
                    y=[row['capex']],
                    marker=dict(color=list(milestone_colors.values())[idx]),
                    text=f"${row['capex']:.1f}M",
                    textposition='outside',
                    name=row['milestone'],
                    showlegend=True,
                    legendgroup='timeline',
                    hovertemplate=f"<b>{row['milestone']}</b><br>Capex: ${row['capex']:.1f}M<extra></extra>"
                ),
                row=1, col=2
            )

        # PANEL 3: Risk Gauge (Bottom-left)
        risk_color_map = {
            'LOW RISK': '#2ecc71',
            'MEDIUM RISK': '#f39c12',
            'HIGH RISK': '#e67e22',
            'VERY HIGH RISK': '#c0392b'
        }
        gauge_color = risk_color_map.get(self.risk_category, '#e74c3c')

        fig.add_trace(
            go.Indicator(
                mode="gauge+number",
                value=self.risk_score,
                domain={'x': [0.05, 0.95], 'y': [0.1, 0.9]},
                title={'text': "Risk Score", 'font': {'size': 14}},
                number={'font': {'size': 40}},
                gauge={
                    'axis': {'range': [0, 3.5], 'tickwidth': 1, 'tickcolor': "darkgray"},
                    'bar': {'color': gauge_color, 'thickness': 0.75},
                    'bgcolor': "white",
                    'borderwidth': 2,
                    'bordercolor': "gray",
                    'steps': [
                        {'range': [0, 0.25], 'color': '#d5f4e6'},
                        {'range': [0.25, 0.4], 'color': '#fdeaa8'},
                        {'range': [0.4, 0.55], 'color': '#fadbd8'},
                        {'range': [0.55, 3.5], 'color': '#f5b7b1'}
                    ],
                    'threshold': {
                        'line': {'color': "red", 'width': 4},
                        'thickness': 0.75,
                        'value': 0.55
                    }
                }
            ),
            row=2, col=1
        )

        # PANEL 4: Capital Structure Pie (Bottom-right)
        cs = capital_structure
        share_categories = ['Basic Shares', 'Options', 'Warrants']
        share_values = [
            cs['basic_shares'],
            cs['basic_shares'] * (cs['current_dilution_pct'] / 100) * 0.33,
            cs['basic_shares'] * (cs['current_dilution_pct'] / 100) * 0.67
        ]

        fig.add_trace(
            go.Pie(
                labels=share_categories,
                values=share_values,
                marker=dict(colors=['#3498db', '#9b59b6', '#e74c3c']),
                textposition='inside',
                textinfo='label+percent',
                textfont=dict(size=12, color='white'),
                hovertemplate='<b>%{label}</b><br>%{value:.1f}M shares (%{percent})<extra></extra>',
                showlegend=False
            ),
            row=2, col=2
        )

        # Update layout with explicit sizing
        fig.update_layout(
            title={
                'text': f"<b>{self.company_name} ({self.ticker}) - Dilution Risk Dashboard</b><br>" +
                        f"<sub>Evaluation: {self.evaluation_date} | Risk: {self.risk_category} (Score: {self.risk_score:.2f})</sub>",
                'x': 0.5,
                'xanchor': 'center',
                'font': {'size': 18, 'color': '#2c3e50'}
            },
            height=900,
            width=1400,
            hovermode='closest',
            plot_bgcolor='#f8f9fa',
            paper_bgcolor='white',
            font=dict(family='Arial, sans-serif', size=11, color='#2c3e50'),
            legend=dict(
                orientation="h",
                yanchor="bottom",
                y=-0.15,
                xanchor="center",
                x=0.5
            )
        )

        # Update axes for panel 1
        fig.update_xaxes(title_text="Capital ($ Millions)", row=1, col=1, showgrid=True, gridcolor='lightgray')
        fig.update_yaxes(row=1, col=1, showgrid=False)

        # Update axes for panel 2
        fig.update_xaxes(title_text="Quarter", row=1, col=2, showgrid=False)
        fig.update_yaxes(title_text="Capex ($M)", row=1, col=2, showgrid=True, gridcolor='lightgray')

        return fig

    def save_widget(self, fig, filename='dilution_dashboard.html'):
        """Save as standalone HTML"""
        fig.write_html(filename, include_plotlyjs='cdn', config={'displayModeBar': True})
        print(f"Dashboard saved: {filename}")
        return filename

    def display_widget(self, fig):
        """Display in notebook/browser"""
        fig.show()


# TEST THE FIXED VERSION
if __name__ == "__main__":
    dashboard = CappyDilutionDashboard(
        company_name="1911 Gold Corporation",
        ticker="AUMB.V",
        evaluation_date="November 7, 2025",
        risk_score=2.59,
        risk_category="VERY HIGH RISK"
    )

    financial_data = {
        'cash': 15.2,
        'monthly_burn': 1.5,
        'capex_need': 21.5,
        'shortfall': 6.3
    }

    timeline_data = [
        {'quarter': 'Q4 2025', 'milestone': 'PEA Complete', 'capex': 0.8},
        {'quarter': 'Q1 2026', 'milestone': 'Trial Mining Starts', 'capex': 2.0},
        {'quarter': 'Q2-Q3 2026', 'milestone': 'Bulk Sample Phase', 'capex': 17.5},
        {'quarter': 'H2 2026', 'milestone': 'PFS Development', 'capex': 8.0}
    ]

    capital_structure = {
        'basic_shares': 262.32,
        'diluted_shares': 296.19,
        'current_dilution_pct': 12.9
    }

    fig = dashboard.create_dashboard(financial_data, timeline_data, capital_structure)
    dashboard.save_widget(fig, 'AUMB_fixed_dashboard.html')
    print("✓ 4-panel dashboard generated successfully")
```

### How to Call Within Cappy Workflow

After Phase 3 analysis completes and risk score is calculated:

```python
# At end of PHASE 3 section, before final output generation:

dashboard_output = generate_cappy_output_with_dashboard(
    ticker=analysis_ticker,
    company_name=analysis_company,
    evaluation_date=analysis_date,
    risk_data={
        'risk_score': composite_risk_score,
        'risk_category': risk_category_label,
        'probability_of_raise_next_6q': prob_raise,
        'expected_dilution_percent': expected_dilution
    },
    financial_data={
        'cash': phase1_cash_position,
        'monthly_burn': phase1_monthly_burn,
        'capex_need': phase2_total_capex,
        'shortfall': phase2_funding_gap
    },
    timeline_data=phase2_project_milestones,  # List of dicts with quarter, milestone, capex
    capital_structure={
        'basic_shares': phase3_basic_shares,
        'diluted_shares': phase3_diluted_shares,
        'current_dilution_pct': phase3_dilution_pct
    }
)

# Include in final output JSON
output_json['dashboard'] = {
    'widget_file': dashboard_output['dashboard_file'],
    'embedded_html': dashboard_output['dashboard_html']
}
```

### Output Integration

The dashboard is now generated as:
1. **Standalone HTML file** (e.g., `AUMB_dilution_dashboard.html`) - can be emailed or uploaded
2. **Embedded HTML string** - for inclusion in reports, Markdown, or web pages
3. **JSON representation** - for API responses

### Usage in Final Report

In the narrative/report generation phase:

```markdown
## Dilution Risk Dashboard

[Dashboard widget embedded below - interactive in HTML exports]

[EMBEDDED_DASHBOARD_HTML]

This interactive dashboard visualizes:
- **Top-left**: Cash position vs. capex requirements (showing funding gap)
- **Top-right**: Project timeline with capital milestones
- **Bottom-left**: Risk score gauge with threshold indicators  
- **Bottom-right**: Capital structure showing current dilution breakdown
```

---

# Junior Mining Dilution Risk Forecasting RAG Agent - Model Specification v2.5

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

---

## Data Completeness Flag

```json
"data_completeness": "HIGH" | "MEDIUM" | "LOW",
"completeness_notes": "All Tier 1 sources current within 90 days; insider data through 2025-11-07"
```

---

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

# SECTION 5: PRACTICAL IMPLEMENTATION GUIDE FOR USERS

## How to Use Cappy v2.5 Efficiently

### Quick Analysis (Low Token Cost, ~5K tokens)

**Goal:** Get dilution risk score in <2 hours

**Steps:**
1. Provide: Ticker + evaluation date
2. Cappy retrieves: Most recent 10-Q/MD&A + latest 8-K/Material Facts
3. Cappy runs: Capital runway calculation + insider signal check
4. Output: 1-paragraph risk assessment + confidence score

**Example Query:**
> "Quick dilution assessment for EU (Encore Energy). Use certified financials only. Output: risk score, runway, confidence."

---

### Standard Analysis (Medium Token Cost, ~15K tokens)

**Goal:** Full dilution forecast with capital plans context

**Steps:**
1. Provide: Ticker + evaluation date
2. Cappy retrieves: Phase 1 + Phase 2 data (financials + earnings calls)
3. Cappy runs: Capital runway + management guidance analysis
4. Output: Dilution probability, magnitude, timing, recommendations

**Example Query:**
> "Full dilution analysis for EU. Include Phase 1 + Phase 2 (earnings call transcripts). Output: CSV with risk metrics, capital timeline, insider signals."

---

### Deep Analysis (Full Token Cost, ~18K tokens)

**Goal:** Comprehensive dilution forecast with peer and macro context

**Steps:**
1. Provide: Ticker + evaluation date
2. Cappy retrieves: Phase 1 + Phase 2 + Phase 3 (including peer context)
3. Cappy runs: All analyses + peer comparisons + macro scenario
4. Output: Complete risk profile, social media post, investment thesis

**Example Query:**
> "Complete dilution analysis for EU. Include Phase 1, 2, and 3 with peer uranium comparisons. Output: Full report + LinkedIn post + validation checklist."

---

## Pre-Query Checklist: What to Specify

**Before running Cappy, provide:**

- [ ] Company ticker
- [ ] Evaluation date
- [ ] Analysis type: "Quick" / "Standard" / "Deep"
- [ ] Output format: "CSV" / "Narrative" / "Social Media" / "All"
- [ ] Token budget (optional): "Max 10K tokens" or leave empty for standard
- [ ] Additional context (optional): "Recent warrant exercise announced" or "Pending FDA approval"

---

# SECTION 6: COMMON PITFALLS & HOW TO AVOID THEM

## Pitfall 1: Searching General Web Before Checking SEC/SEDAR+

**Wrong:**
```
Search: "[ticker] dilution risk" (Google general web)
Result: BlogSpam, Reddit, outdated articles
Token Cost: 5K+ wasted tokens
```

**Right:**
```
Search: "[ticker] 10-Q site:sec.gov" (official filing)
Result: Certified financial data
Token Cost: 2K tokens, high quality
```

---

## Pitfall 2: Missing Recent Capital Activity (8-K / Material Facts)

**Wrong:**
- Retrieve 10-Q dated 2025-08-10
- Conclude "cash runway through late 2026"
- Miss: $115M convertible announced 2025-08-21 (11 days AFTER 10-Q)
- Result: Forecast is wrong; dilution pressure overstated

**Right:**
- Retrieve 10-Q dated 2025-08-10
- Search for 8-K/Material Facts filed after 2025-08-10
- Find: $115M convertible announced 2025-08-21
- Conclusion: Cash runway now through 2026; dilution pressure LOW near-term

**Action:** Always search 8-K/Material Facts in last 90 days; don't rely on 10-Q alone.

---

## Pitfall 3: Skipping Earnings Call Transcripts

**Wrong:**
- Use only 10-Q MD&A for capital plans
- Result: Miss management's explicit statement: "We need capital by Q4 2026"
- Forecast: Incomplete; timing estimates unreliable

**Right:**
- Extract capital plans from 10-Q MD&A
- Confirm/refine with earnings call transcript
- Result: Specific timeline from CFO ("runway through Q3 2026")
- Forecast: Accurate timing; high confidence

**Action:** Always retrieve last 2-3 earnings calls for capital plans validation.

---

## Pitfall 4: Misinterpreting Insider Transactions

**Wrong:**
- CEO sold 50K shares last month = "dilution imminent"
- Result: False positive alert

**Right:**
- CEO has been selling steadily (monthly sales, regular pattern) = normal diversification
- CEO + CFO BOTH sold 100K shares in same week = stress signal
- Result: Accurate assessment of insider sentiment

**Action:** Look for patterns and acceleration, not single transactions.

---

# SECTION 7: APPENDIX - QUICK REFERENCE SEARCH QUERIES

## U.S. SEC Filings (Copy & Paste Ready)

```
# Most Recent 10-Q
[ticker] 10-Q site:sec.gov

# Most Recent 10-K
[ticker] 10-K site:sec.gov

# Recent 8-K filings (capital-related)
[ticker] 8-K capital site:sec.gov
[ticker] 8-K convertible site:sec.gov
[ticker] 8-K acquisition site:sec.gov

# Insider transactions (FORM 4)
[ticker] form 4 site:sec.gov

# Prospectus for convertible/warrant offerings
[ticker] prospectus 424B site:sec.gov

# Proxy statement (DEF 14A)
[ticker] DEF 14A site:sec.gov
```

---

## Canada SEDAR+ Filings (Copy & Paste Ready)

```
# Quarterly MD&A
[ticker] quarterly MD&A site:sedarplus.ca

# Annual Financial Statements
[ticker] annual financial statements site:sedarplus.ca

# Material Change Reports
[ticker] material change site:sedarplus.ca

# Prospectus / Offering Circular
[ticker] prospectus site:sedarplus.ca

# NI 43-101 Technical Reports
[company name] NI 43-101

# Insider Transactions (SEDI)
[ticker] SEDI insider trading site:sedi.ca
```

---

## Earnings Call Transcripts & News

```
# Seeking Alpha transcript
[ticker] earnings call transcript site:seekingalpha.com

# Company IR page
[ticker] earnings call site:[company domain]/investor

# Press releases
[ticker] press release site:[company domain]

# Industry news
[ticker] uranium financing site:mining.com
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
