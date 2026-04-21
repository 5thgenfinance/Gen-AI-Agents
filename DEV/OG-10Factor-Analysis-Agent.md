ni_51_101_sourcing_workflow:
  description: Automated NI 51-101 reserve filings discovery, extraction, validation,
    and integration workflow
  version: '1.0'
  last_updated: '2025-11-14'
  application: Extract proved, probable, possible reserves by product type from latest
    NI 51-101 filings
  regulatory_framework: National Instrument 51-101 (NI 51-101) - Standards of Disclosure
    for Oil and Gas Activities (Canada)
  target_documents:
  - Form 51-101F1 - Statement of Reserves Data and Other Oil and Gas Information
  - Annual Information Form (AIF) - containing NI 51-101 disclosure
  - Management Discussion & Analysis (MD&A) - reference to reserves
  workflow_phases:
    phase_1:
      name: Company Identification & Filing Source Location
      description: Identify target company and locate primary/secondary/tertiary filing
        sources
    phase_2:
      name: Latest Filing Discovery
      description: Search for most recent NI 51-101 filing across all sources with
        intelligent fallback
    phase_3:
      name: Document Download & Acquisition
      description: Download and store latest filing document locally
    phase_4:
      name: Data Extraction & Parsing
      description: Extract reserve data tables by product type and reserve category
    phase_5:
      name: Data Validation & Completeness Check
      description: Validate data integrity, completeness, and consistency
    phase_6:
      name: Data Integration & Output
      description: Format and export data for downstream processing
  data_sources:
    primary_sources:
    - name: SEDAR+ (System for Electronic Document Analysis and Retrieval Plus)
      url: https://www.sedarplus.ca
      search_endpoint: https://www.sedarplus.ca/search/
      description: Primary Canadian securities filing repository; mandatory for all
        Canadian oil & gas reporting issuers
      document_type: Form 51-101F1 OR Annual Information Form (AIF) containing 51-101
        disclosure
      access_type: Public web portal + API (free)
      search_method: Company name, ticker symbol, or CIK number
      filter_category: Oil and Gas Annual Disclosure (NI 51-101)
      typical_filing_lag: 120 days after fiscal year-end (required by regulation)
      reliability: Authoritative - Regulatory mandate
      api_available: true
      api_docs: https://www.sedarplus.ca/about/api-access
      rate_limit: 100 requests per minute
      auth_required: false
    - name: Company Investor Relations Website
      url: Company-specific (e.g., www.cardinalenergy.ca/investor-relations)
      search_pattern: /investor-relations/ OR /shareholders/ OR /sec-filings/ OR /financial-reports/
      description: Direct company source; often posts NI 51-101 filings before SEDAR+
        aggregation
      document_type: Annual Information Form (AIF) with embedded NI 51-101F1
      access_type: Public website
      search_method: Web scraping investor relations page for latest annual/year-end
        documentation
      filter_keywords:
      - 51-101
      - reserves
      - annual report
      - AIF
      - year-end
      - Form F1
      typical_posting_lag: 5-30 days after SEDAR+ filing (sometimes earlier)
      reliability: High - Authoritative source
      web_scraping_required: true
      pdf_parsing_required: true
    secondary_sources:
    - name: Alberta Securities Commission (ASC)
      url: https://www.asc.ca
      description: Provincial regulator for Alberta-domiciled companies; some filings
        available here
      document_type: NI 51-101F1, Form 51-101F2 (Reserve Evaluator Report)
      access_type: Public portal
      search_method: Company search by name or registration number
      reliability: High - Regulatory source
    - name: Company Press Releases / News
      url: Company website news section
      description: Companies often issue press releases summarizing year-end reserves
      document_type: Press release with reserve summary tables
      access_type: Public website
      reliability: Medium - Summary only; requires cross-reference with official filings
  workflow_steps:
    step_1:
      step_number: 1
      name: Company Identification & Source Mapping
      description: Identify target company and map available filing sources
      inputs:
      - Company name (string)
      - Ticker symbol (optional)
      - CIK/Registration number (optional)
      - Primary jurisdiction (Canada/US/Global)
      actions:
      - Validate company name against known oil & gas producers database
      - Verify company is NI 51-101 reporting issuer (Canadian company with oil &
        gas operations)
      - Determine fiscal year-end date (typically December 31, June 30, or March 31)
      - Calculate expected NI 51-101 filing deadline (fiscal year-end + 120 days)
      - 'Initialize source priority list: [SEDAR+, Company Website, ASC, News/Press
        Release]'
      - Build company-specific URLs for Investor Relations page
      outputs:
      - company_id (unique identifier)
      - company_name
      - ticker_symbol
      - cik_number
      - primary_jurisdiction
      - fiscal_year_end_date
      - expected_filing_deadline
      - source_priority_list
      - ir_website_url
      error_handling: If company not found, return error and request valid company
        name/ticker
      validation: Confirm company has oil & gas operations in Canada
    step_2:
      step_number: 2
      name: Latest Filing Discovery & Acquisition
      description: Search for most recent NI 51-101 filing across primary/secondary
        sources
      actions:
      - 'Query SEDAR+ API: search("company_name", document_type="NI 51-101")'
      - Filter results by filing date (most recent first)
      - 'Extract filing metadata: filing_date, document_url, filing_status'
      - Verify filing document is NOT a preliminary or superseded filing
      - If SEDAR+ search unsuccessful or filing too old (>18 months), proceed to Company
        Website search
      - Query Company Investor Relations page for latest AIF/Annual Report
      - Parse company website for NI 51-101 disclosure documents
      - If still unsuccessful, check ASC portal for Alberta-domiciled companies
      - Extract latest press release summarizing year-end reserves as fallback
      outputs:
      - filing_url (primary document URL)
      - filing_date
      - filing_document_type (Form 51-101F1 vs AIF vs Press Release)
      - filing_source (SEDAR+ vs Company Website vs ASC vs News)
      - fallback_source_used (true/false)
      - fiscal_year_covered
      - document_status (final/preliminary/superseded)
      search_fallback_chain:
      - 1. SEDAR+ Form 51-101F1 (most authoritative)
      - 2. SEDAR+ AIF (contains 51-101 disclosure)
      - 3. Company Investor Relations AIF page
      - 4. Company Investor Relations press release
      - 5. ASC portal (if Alberta company)
      - 6. Financial news press release
      error_handling: If no filing found within 18 months, alert operator; set status
        to FILING_NOT_FOUND
      validation: Verify document is not preliminary or superseded; filing date reasonable
    step_3:
      step_number: 3
      name: Document Download & Local Storage
      description: Download and store latest NI 51-101 filing document
      actions:
      - Attempt HTTP GET request to filing_url with timeout=30 seconds
      - Verify HTTP 200 response
      - Determine file format (PDF, HTML, XLSX, XML)
      - Download file to local cache directory
      - Calculate file hash (SHA256) for integrity verification
      - 'Store metadata: file_path, file_size, download_timestamp, file_hash'
      - If download fails, retry up to 3 times with exponential backoff
      - If persistent failure, log error and attempt alternate filing_url from fallback
        chain
      outputs:
      - file_path (local storage location)
      - file_size (bytes)
      - file_format (pdf/html/xlsx)
      - download_timestamp
      - file_hash (SHA256)
      - download_status (success/failed/retried)
      error_handling: Retry up to 3 times; if failed, use cached version if available;
        else alert
      cache_management: 'Cache TTL: 30 days; update if newer filing detected'
    step_4:
      step_number: 4
      name: Data Extraction & Parsing by Product Type
      description: Extract reserve data tables from document by product type and reserve
        category
      extraction_methods:
        pdf_documents: PDF table extraction using pdfplumber or tabula-py
        html_documents: HTML table parsing using BeautifulSoup
        excel_documents: Direct spreadsheet parsing using pandas/openpyxl
      product_type_mapping:
        Light_Medium_Oil:
        - light crude
        - light oil
        - medium oil
        - edmonton light
        - wti equivalent
        Heavy_Oil:
        - heavy oil
        - heavy crude
        - wcs
        - western canada select
        - cold lake
        Bitumen:
        - bitumen
        - oil sands
        - unconventional oil
        - athabasca
        Natural_Gas_Liquids:
        - ngls
        - natural gas liquids
        - condensate
        - propane
        Conventional_Natural_Gas:
        - natural gas
        - conventional gas
        - associated gas
        - solution gas
        Synthetic_Crude:
        - synthetic crude
        - sco
        - upgraded bitumen
      reserve_category_mapping:
        Proved_Developed_Producing:
        - pdp
        - proven developed producing
        Proved_Developed_Non_Producing:
        - pdnp
        - proven developed non-producing
        Proved_Undeveloped:
        - pud
        - proven undeveloped
        Total_Proved:
        - total proved
        - 1p
        Probable:
        - probable
        - p50
        Total_Proved_Probable:
        - total 2p
        - proved and probable
        Possible:
        - possible
        - p10
        Total_3P:
        - total 3p
      unit_conversion:
        mmbbl_to_boe: 1.0
        bcf_to_boe: 0.001667
        thousand_bbl_to_boe: 0.001
        mcf_to_boe: 1.667e-06
      outputs:
      - reserves_data_table (DataFrame with product_types × reserve_categories)
      - units_metadata (units for each cell)
      - extraction_confidence_score (0-100%)
      - product_types_found (list)
      - reserve_categories_found (list)
      - missing_cells_count
      error_handling: If table not found, attempt manual extraction or flag for human
        review
      validation: Check data types, units consistency, value ranges (volumes should
        be positive)
    step_5:
      step_number: 5
      name: Data Validation & Completeness Check
      description: Validate extracted data for integrity, consistency, and completeness
      validation_rules:
      - name: Column Completeness
        rule: 'All major categories present: [PDP, PDNP, PUD, Total_Proved, Probable,
          Total_2P]'
        severity: CRITICAL
      - name: Row Completeness
        rule: 'Primary product types represented: [Light_Oil, Heavy_Oil, Natural_Gas,
          NGLs]'
        severity: HIGH
      - name: Mathematical Consistency
        rule: PDP + PDNP + PUD = Total_Proved (within 1% tolerance)
        severity: HIGH
      - name: Category Ordering
        rule: Total_Proved >= (PDP + PDNP + PUD) AND Total_2P >= (Total_Proved + Probable)
        severity: CRITICAL
      - name: Non-negative Values
        rule: All volumes >= 0
        severity: CRITICAL
      - name: Reasonable Ranges
        rule: 'Oil: 1-5000 MMbbl; Gas: 0.1-50000 Bcf; NGLs: 0.1-500 MMbbl'
        severity: MEDIUM
      - name: Year-over-Year Consistency
        rule: Flag >30% annual changes for investigation
        severity: MEDIUM
      - name: Unit Consistency
        rule: All volumes within same product type use consistent units
        severity: HIGH
      - name: Data Type Validity
        rule: All volume fields are numeric (float)
        severity: CRITICAL
      - name: Null/Missing Data
        rule: Missing < 5% of total cells
        severity: HIGH
      outputs:
      - validation_status (COMPLETE_AND_VALID / REVIEW_REQUIRED / CRITICAL_ISSUES
        / INCOMPLETE)
      - validation_score (0-100)
      - validation_report (detailed findings)
      - critical_failures (list)
      - high_failures (list)
      - timestamp
      alert_conditions:
      - 'If CRITICAL_ISSUES: Alert operator immediately; do not proceed'
      - 'If REVIEW_REQUIRED: Log warning; flag for manual review'
      - 'If INCOMPLETE: Notify operator; attempt to locate missing data'
      error_handling: If validation fails, store failed records for manual review;
        do not pass to downstream
    step_6:
      step_number: 6
      name: Data Integration & Structured Output
      description: Format validated data and export for downstream processing
      actions:
      - 'If validation_status != CRITICAL_ISSUES: proceed'
      - Normalize all volumes to BOE (barrels of oil equivalent)
      - Create standardized output schema (JSON/CSV)
      - 'Generate metadata: company, fiscal_year, filing_date, effective_date, evaluator,
        extraction_timestamp'
      - 'Create audit trail: source_url, file_path, extraction_method, validation_status'
      - 'Export to multiple formats: JSON, CSV, Parquet'
      - Store outputs to data warehouse
      - Generate data quality report for compliance documentation
      output_formats:
      - JSON (machine-readable, API-compatible)
      - CSV (spreadsheet-compatible, for manual review)
      - Parquet (optimized for analytics)
      downstream_integration:
      - Pass reserves_data_json to commodity pricing workflow
      - Pass reserves_data_json to plausible reserves calculator (formula library)
      - Pass reserves_data_json to NPV valuation module
      - Store in data warehouse for historical tracking
      - Integration with portfolio analytics dashboard
      error_handling: If export fails, log error and alert; retry up to 3 times
      data_retention: Keep all versions for historical comparison; purge after 3 years
  error_handling_strategies:
    document_not_found:
      error: Latest NI 51-101 filing cannot be located
      causes:
      - Filing not yet published (within 120-day window)
      - Company delisted or ceased operations
      - Company is US-domiciled, not Canadian
      recovery_actions:
      - Alert operator with company name and expected filing deadline
      - 'Check ASC/SEDAR+ status: is company still active?'
      - Check for provisional/preliminary filing
      - Check SEC EDGAR if US company (fallback)
      - Set status to FILING_PENDING or COMPANY_NOT_FOUND
    download_failure:
      error: Cannot download filing document from URL
      causes:
      - URL broken or document moved
      - Network connectivity issue
      - Server timeout
      - PDF protected or non-downloadable
      recovery_actions:
      - Retry up to 3 times with exponential backoff (1s, 5s, 30s)
      - Try alternate filing_url from fallback chain
      - Use company website as fallback source
      - If all downloads fail, alert operator and skip to manual retrieval
    extraction_failure:
      error: Cannot parse reserve data from document
      causes:
      - Document format unexpected (image-only PDF, old PDF format)
      - Reserve table structure non-standard
      - OCR required for scanned document
      - Company uses proprietary table format
      recovery_actions:
      - Attempt OCR if scanned PDF detected
      - Try alternate extraction tool (tabula vs pdfplumber vs manual)
      - Flag for manual extraction by human reviewer
      - Search for press release summary as interim data source
      - Set extraction_status to MANUAL_REVIEW_REQUIRED
    validation_failure:
      error: Extracted data fails validation checks
      causes:
      - Missing reserve categories
      - Mathematical inconsistencies
      - Data type errors
      - Outlier values
      recovery_actions:
      - 'If CRITICAL failures: alert operator; do not proceed to valuation'
      - 'If HIGH failures: log for review; attempt to identify/fix root cause'
      - 'For missing data: attempt to extract from alternate sections of document'
      - 'For math errors: recalculate totals if subcategories present'
      - 'For outliers: flag for manual review; use conservative estimate'
  monitoring_alerts:
    alert_conditions:
    - condition: Filing not found within 150 days of fiscal year-end
      severity: HIGH
      action: Alert operator; check if company still active or filing delayed
    - condition: Data extraction fails (all tables not parseable)
      severity: CRITICAL
      action: Flag for manual extraction; do not attempt valuation with incomplete
        data
    - condition: Validation status = CRITICAL_ISSUES
      severity: CRITICAL
      action: Alert operator immediately; pause downstream processing
    - condition: Validation score < 70%
      severity: HIGH
      action: Alert for manual review; data quality insufficient for high-confidence
        valuation
    - condition: Year-over-year reserve change > 30% without M&A activity
      severity: MEDIUM
      action: Flag for investigation; verify not data extraction error
    - condition: Missing product type (e.g., Heavy Oil for company known to produce
        it)
      severity: HIGH
      action: Alert; attempt to locate data in alternate sections or prior years
    notification_channels:
    - Log file (JSON format)
    - Email alert to operator (if critical)
    - Dashboard notification
    - Data warehouse quality flag
  performance_metrics:
    track:
    - End-to-end workflow execution time (seconds)
    - Document download time (seconds)
    - Data extraction time (seconds)
    - Validation time (seconds)
    - Extraction success rate (%)
    - Validation pass rate (%)
    - Average extraction_confidence_score (%)
    - Average validation_score (%)
    - Alerts generated (count)
    - Manual review required (count)
    dashboard_reporting: Track metrics by company, by month, by source for optimization
  execution_schedule:
    trigger: Automatic or manual invocation
    frequency: 'Daily check for new filings (during filing season: Nov-May for calendar
      year-end companies)'
    batch_processing: Process multiple companies in parallel (up to 10 concurrent
      workflows)
    storage: All outputs retained indefinitely in data warehouse for historical comparison
    archival: Move to cold storage after 3 years

---


# Oil & Gas Stock Scoring Framework
## 10-Factor Model for Upstream E&P Companies

---

### 1) Reserve Base & Asset Quality
- Weight: 10%
- Primary Sources: SEC 10-K, 20-F, Prospectus; Reserve estimates (third-party audits preferred)
- Key Metrics: Proved (1P), Proved+Probable (2P), Total Resources (BOE); reserve life index; reserve replacement ratio
- Analysis Focus: Reserve size/quality; production decline rates; geographic diversification; jurisdiction tier; infrastructure access; exploration/development upside; resource risk classification
- Scoring (1–10):
  - 9–10: World-class reserves (500M+ BOE 1P), >15-year RLI, >90% tier 1 assets, strong infrastructure, liquids-weighted (60%+ oil/condensate); concentration in high-return tier-1 assets is a strength
  - 7–8: High-quality reserves (250–500M BOE 1P), 10–15 year RLI, majority tier 1/2 assets, good infrastructure, 50–60% liquids
  - 5–6: Moderate reserves (100–250M BOE 1P), 8–10 year RLI, mixed tier 1/2 assets, adequate infrastructure, 40–50% liquids or gas-weighted composition
  - 3–4: Limited reserves (<100M BOE 1P), <8 year RLI, significant tier 2/3 exposure, infrastructure gaps, heavy gas/NGL weighting
  - 1–2: Poor reserve base (<50M BOE 1P), <5 year RLI, majority tier 4–5 jurisdictions, high depletion risk, limited reserve replacement optionality

---

### 2) People / Management Team
- Weight: 10%
- Focus: Track record in E&P; reserve replacement success; well drilling success rates; production execution; cost control; capital allocation discipline; shareholder communication; governance
- Key Metrics: Historical reserve replacement ratio (target >100%); wells drilled and success rate; projects completed on time/budget; insider ownership; board composition
- Scoring (1–10):
  - 9–10: Proven track record of reserve growth, >100% RRR, excellent ops discipline, transparent comms, >25% insider ownership, strong board
  - 7–8: Strong experience in E&P, RRR ~100%, good ops history, transparent disclosures, 10–25% insider ownership
  - 5–6: Mixed record, RRR 80–100%, adequate technical depth, reasonable comms, 1–10% insider ownership
  - 3–4: Limited E&P experience, RRR <80%, poor cost control, weak comms, minimal insider ownership
  - 1–2: No relevant upstream experience, repeated project failures, poor governance, no insider alignment

---

### 3) Share Structure & Dilution Risk
- Weight: 10%
- Focus: Fully diluted shares outstanding; BOE per share; future dilution from capex; insider ownership; stock buyback plans; financing terms
- Key Calcs:
  - BOE Per Share = Total Proved Reserve BOE / Fully Diluted Shares
  - Capex-Induced Dilution = Est. new shares at reserve replacement / Current FDS
  - Future FDS = Current FDS × (1 + Capex Dilution %)
- Scoring (1–10):
  - 9–10: <50M FDS, >0.5 BOE/share, high insider, minimal dilution risk (<5%), active buyback program
  - 7–8: 50–150M FDS, 0.3–0.5 BOE/share, reasonable insider, manageable dilution (5–10%)
  - 5–6: 150–400M FDS, 0.1–0.3 BOE/share, moderate insider, expected dilution 10–20% from capex
  - 3–4: 400M–1B FDS, <0.1 BOE/share, low insider, significant dilution risk (20–50%)
  - 1–2: >1B FDS, minimal insider, severe future dilution (>50%)

---

### 4) Geographic & Political Risk
- Weight: 10%
- Focus: Political stability; government/policy environment; permitting/regulatory frameworks; tax regime; infrastructure maturity; community relations; currency/sovereign risk; concentration
- Tier Classification:
  - Tier 1: Canada, United States, Australia
  - Tier 2: Mexico, Chile, Norway, Brazil
  - Tier 3: Argentina, Colombia, select African producers
  - Tier 4–5: Higher-risk jurisdictions (policy uncertainty, regulatory instability, sovereign risk)
- Scoring (1–10):
  - 9–10: 80%+ reserves in Tier 1, stable policy, predictable permitting, strong rule of law
  - 7–8: 60–80% Tier 1, generally stable, established regulatory framework
  - 5–6: 40–60% Tier 1, mixed jurisdictions, some policy headwinds
  - 3–4: <40% Tier 1, significant Tier 3–4 exposure, regulatory/policy uncertainty
  - 1–2: Majority in Tier 4–5, high political risk, unstable environment

---

### 5) Production Growth & Reserve Replacement
- Weight: 10%
- Focus: Reserve replacement ratio (RRR); production growth trajectory; project pipeline (committed/sanctioned/planned); finding & development costs (F&D); time-to-first-production; capex efficiency; guidance visibility
- Key Calcs:
  - RRR = Reserves Added (organic) / Current Year Production (BOE)
  - Growth Rate = (Future Production − Current) / Current × 100
  - Recycle Ratio = Average Netback / F&D Cost (target >1.5x)
  - Production CAGR (next 5 years)
- Scoring (1–10):
  - 9–10: RRR >150%, 5-year production CAGR >10%, strong project pipeline, F&D <$8/BOE, clear milestones
  - 7–8: RRR 100–150%, CAGR 5–10%, good projects in development, F&D $8–$12/BOE
  - 5–6: RRR 80–100%, CAGR 0–5% (stable), moderate pipeline, F&D $12–$15/BOE
  - 3–4: RRR 50–80%, declining production (>5% annual decline), limited projects, F&D >$15/BOE
  - 1–2: RRR <50%, severe decline, no growth pipeline, F&D undefined or very high

---

### 6) Stock Momentum & Market Sentiment
- Weight: 10%
- Focus: Price trend (vs 30W/200W MA); volume trends; analyst coverage/sentiment; insider buying/selling; relative strength; forward multiples vs peers
- Key Metrics: Stock price position (above/below key moving averages); volume trends; analyst recommendations; insider transactions; peer relative performance
- Scoring (1–10):
  - 9–10: Strong uptrend, price above 200W MA, increasing volume, positive analyst sentiment, insider buying, peer outperformance
  - 7–8: Constructive pattern, above 30W MA, stable/increasing volume, mixed-to-positive coverage, some insider interest
  - 5–6: Neutral pattern, near moving averages, average volume, mixed analyst sentiment
  - 3–4: Weak trend, below 30W MA, declining volume, negative coverage, insider selling signals
  - 1–2: Downtrend, below 200W MA, poor reception, weak technicals, weak peer relative strength

---

### 7) Cost Structure & Operating Leverage
- Weight: 10%
- Focus: All-in cash cost per BOE (operating + capex); netback margins; finding & development costs; FCF generation at various commodity prices; cost inflation resilience; operating leverage
- Key Calcs:
  - All-In Cost (AIC) per BOE = (Operating Costs + Capex) / Annual Production BOE
  - AIC % of Price = 100% × (AIC / WTI or Brent price)
  - Netback per BOE = Selling Price − Operating Costs
  - FCF = (Production BOE × Netback) − Capex − Exploration
  - Operating Leverage = % Change in FCF / % Change in Oil Price
- Scoring (1–10):
  - 9–10: All-in costs <35% of commodity price, netback >$40/BOE (normalized), strong FCF even at $50/bbl, >2.0× leverage
  - 7–8: 35–50% of price, netback $25–$40/BOE, positive FCF at $60+/bbl, 1.5–2.0× leverage
  - 5–6: 50–70% of price, netback $15–$25/BOE, marginal FCF at current prices, 1.0–1.5× leverage
  - 3–4: 70–90% of price, netback $5–$15/BOE, minimal FCF, breakeven or negative leverage
  - 1–2: >90% of price, negative or razor-thin netback, negative FCF at commodity reality, poor leverage

---

### 8) Balance Sheet Strength & Liquidity
- Weight: 10%
- Focus: Net debt position; leverage ratios; debt maturity profile; liquidity access (RBL capacity); debt covenants; working capital; cash flow stability; ability to fund capex and dividends organically
- Key Calcs:
  - Net Debt = Total Debt − Cash & Equivalents
  - Debt-to-EBITDAX = Net Debt / EBITDAX (typical E&P lender max: 4.0x)
  - Debt-to-Equity = Total Debt / Total Equity
  - Interest Coverage = EBITDAX / Interest Expense
  - FCF Coverage = FCF / Debt Service (principal + interest)
  - Liquidity = Cash + Undrawn RBL Capacity
- Scoring (1–10):
  - 9–10: Net cash or <1.0x Debt/EBITDAX, D/E <20%, >24 months liquidity, no near-term maturities, investment-grade trajectory
  - 7–8: 1.0–2.0x leverage, D/E 20–40%, >18 months liquidity, manageable debt service, stable covenants
  - 5–6: 2.0–3.0x leverage, D/E 40–60%, ~12 months liquidity, adequate headroom
  - 3–4: 3.0–4.0x leverage, D/E 60–80%, <12 months liquidity, covenant risk in downturn
  - 1–2: >4.0x leverage, D/E >80%, severe liquidity stress, near covenant breaches, distress risk

---

### 9) Valuation & Cash Return Potential
- Weight: 10%
- Focus: Enterprise value relative to reserve base; production rate; FCF multiples; peer-relative valuation; replacement cost of reserves; dividend yield; FCF yield
- Key Calcs:
  - EV/BOE Reserve = Enterprise Value / Total Proved Reserve BOE
  - EV/Production Rate = Enterprise Value / Current Production (boe/d)
  - FCF Multiple = Market Cap / Annual FCF (at normalized commodity prices)
  - FCF Yield = Annual FCF / Market Cap
  - Price-to-NAV = Current Stock Price / Calculated NAV per share (DCF-based)
  - Peer Valuation Comparison (EV/bbl of reserves, FCF multiples, dividend yield)
- Scoring (1–10):
  - 9–10: EV/BOE <$5, deep discount to peers (>30%), FCF yield >8%, trading <0.8x NAV, significant undervaluation
  - 7–8: EV/BOE $5–$10, 15–30% discount to peers, FCF yield 5–8%, 0.8–0.95x NAV
  - 5–6: EV/BOE $10–$15, modest discount to fair value, FCF yield 3–5%, 0.95–1.1x NAV
  - 3–4: EV/BOE $15–$25, trading at or above peer average, FCF yield <3%, 1.1–1.3x NAV
  - 1–2: EV/BOE >$25, significant premium to peers, low/no FCF yield, >1.3x NAV

---

### 10) Upside Potential & Risk-Adjusted Return
- Weight: 10%
- Focus: Composite strength of factors 1–9; thesis conviction; reserve/resource expansion scenarios; multi-year value creation path; downside protection; probability-weighted returns; timing to value realization
- Scenario Guidance:
  - Base Case: WTI $70–$75/bbl, natural gas $3–$4/MMBtu (weighted 50%)
  - Bull Case: WTI $100–$110/bbl, gas $5–$6/MMBtu (weighted 30%)
  - Bear Case: WTI $50–$55/bbl, gas $2–$2.50/MMBtu (weighted 20%)
- Key Calcs (timing discount):
  - If years_to_major_catalyst > 5: discount factor = 0.4
  - 3–5 years: 0.6
  - 1–3 years: 0.85
  - <1 year (production ramp, dividend, M&A catalyst): 1.0
  - Probability-Weighted NPV = (NPV_base × 50%) + (NPV_bull × 30%) + (NPV_bear × 20%)
  - Return Multiple = (Future EV + Dividends Collected) / Current Enterprise Value
  - Risk-Adjusted Return = Return Multiple × Discount Factor
- Scoring (1–10):
  - 9–10: Exceptional thesis, high conviction, ≥8× return potential at normalized prices, strong downside cushion (reserve base, balance sheet), catalyst within 12 months
  - 7–8: Strong upside, ≥5× return potential, moderate risk, good downside protection, catalysts 1–2 years
  - 5–6: Reasonable opportunity, ≥3× return potential, average risk, adequate downside, catalysts 2–3 years
  - 3–4: Marginal, <3× potential, execution risk, limited downside cushion, catalysts >3 years
  - 1–2: Poor opportunity, <1.5× potential, high risk, weak downside protection, unclear catalyst timing

---

## Summary & Implementation Notes

**Total Weight: 100% (10 factors × 10% each)**

### Calculation Workflow:
1. Score each factor on 1–10 scale using provided definitions and metrics
2. Apply 10% weight to each factor score
3. Sum weighted scores for total framework score (0–100 scale)

### Interpretation Guide:
- **80–100:** Strong candidate, high conviction
- **70–79:** Attractive, monitor closely
- **60–69:** Mixed signals, requires deeper analysis
- **50–59:** Weak profile, limited appeal
- **<50:** Avoid or turnaround candidate only

### Key Data Sources:
- Company SEC filings (10-K, 10-Q, 8-K, prospectuses)
- Third-party reserve audits (Ryder Scott, DeGolyer & MacNaughton, etc.)
- Investor presentations and earnings call transcripts
- Commodity price forecasts (consensus, futures markets)
- Peer group benchmarks (similar-sized operators in tier 1/2 jurisdictions)
- Macro energy outlook (EIA, IEA, Bloomberg Energy)

### Customization Considerations:
- **For dividend-focused investors:** Increase weight on factor 8 (balance sheet) and factor 9 (valuation/yield)
- **For growth investors:** Increase weight on factor 5 (growth/RRR) and factor 10 (upside potential)
- **For risk-averse investors:** Increase weight on factor 4 (geography) and factor 8 (balance sheet strength)
- **For value hunters:** Emphasize factor 9 (valuation) and ensure factor 10 includes asymmetric risk/reward
