---
# Royalty and Streaming Company Data Scraper Workflow
# Input: List of royalty company tickers
# Output: JSONL file with asset-level data conforming to schema

name: Royalty_Company_Asset_Scraper
version: 1.0
description: Scrapes asset data from royalty/streaming companies to build NAV model database

# ==============================================================================
# CONFIGURATION
# ==============================================================================
config:

  companies to process:
    - WPM
    - FNV	
  output_format: jsonl
  output_file: royalty_assets_{date}.jsonl
  validation_enabled: true
  metal_prices_source: real_time_api  # or static_file

# ==============================================================================
# STEP 1: GATHER SOURCE DOCUMENTS
# ==============================================================================
step_1_document_collection:
  description: Download and index all source documents for each company

  tasks:
    - name: download_asset_handbooks
      for_each: company in target_companies
      actions:
        - navigate_to: "{company.asset_handbook_url}"
        - find_element: "a[href*='asset-handbook'], a[href*='Asset-Handbook'], a[contains(text(), 'Asset Handbook')]"
        - download_pdf: true
        - save_location: "./data/asset_handbooks/{company.ticker}_asset_handbook.pdf"
        - extract_text: true
        - ocr_if_needed: true

      fallback:
        - search_web: "{company.name} asset handbook PDF latest"
        - download_first_result: true

    - name: download_latest_mdas
      for_each: company in target_companies
      actions:
        - query_sedar_or_sec:
            profile_id: "{company.sedar_profile or company.sec_cik}"
            document_types: ["Management Discussion & Analysis", "MD&A"]
            filing_date_range: "last_4_quarters"
        - download_all_matches: true
        - save_location: "./data/mdas/{company.ticker}/"
        - extract_tables: true
        - priority_sections:
            - "Production and Sales"
            - "Portfolio of Royalties"
            - "Producing Assets"
            - "Development Assets"

    - name: identify_operators
      description: Extract list of all operators mentioned in asset handbooks
      actions:
        - parse_asset_handbooks: true
        - extract_operators:
            fields: ["operator_name", "operator_ticker", "project_name"]
        - create_operator_list: "./data/operators_list.json"

    - name: download_operator_technical_reports
      for_each: operator in operators_list
      actions:
        - query_sedar_or_sec:
            company_name: "{operator.name}"
            document_types: ["NI 43-101", "Technical Report"]
            filing_date_range: "last_24_months"
        - filter_by_project: "{operator.project_name}"
        - download_matches: true
        - save_location: "./data/technical_reports/{operator.name}_{project.name}/"

# ==============================================================================
# STEP 2: PARSE AND EXTRACT ASSET-LEVEL DATA
# ==============================================================================
step_2_asset_extraction:
  description: Extract structured asset data from source documents

  tasks:
    - name: parse_asset_handbook
      for_each: company in target_companies
      input_file: "./data/asset_handbooks/{company.ticker}_asset_handbook.pdf"

      extraction_rules:
        identify_assets:
          - look_for_sections: ["Asset Portfolio", "Principal Assets", "Royalty Portfolio"]
          - identify_tables: true
          - extract_columns:
              - project_name
              - operator_name
              - location_country
              - resource_type
              - royalty_type
              - royalty_rate_percent
              - development_stage

        for_each_asset:
          create_record:
            asset_id: "{company.ticker}-{asset_index}-{project_name_normalized}"
            company_name: "{company.name}"
            company_ticker: "{company.ticker}"
            project_name: "{extracted.project_name}"
            operator_name: "{extracted.operator_name}"
            resource_type: "{extracted.resource_type | parse_to_array}"
            development_stage: "{extracted.development_stage | normalize}"
            royalty_type: "{extracted.royalty_type | normalize}"
            royalty_rate_percent: "{extracted.royalty_rate | parse_number}"
            geography:
              country: "{extracted.location_country}"
              state_province: "{extracted.location_state | null_if_empty}"

        extract_detailed_sections:
          - for_each_asset:
              search_asset_page: "{project_name}"
              extract_fields:
                - mine_life_years
                - reserve_data
                - resource_data
                - production_forecast
                - metal_price_assumptions
                - buyback_provisions
                - production_caps

      output: "./data/extracted/{company.ticker}_assets_raw.json"

    - name: enrich_from_mda
      for_each: company in target_companies
      input_files: 
        - "./data/mdas/{company.ticker}/*.pdf"

      extraction_rules:
        find_production_tables:
          - section_keywords: ["Production and Sales", "GEO", "Gold Equivalent Ounces"]
          - identify_tables: true
          - extract_structure:
              rows: asset_names
              columns: [quarter, year, geo_ounces, revenue]

        match_to_assets:
          - for_each: row in production_table
            fuzzy_match:
              field: project_name
              threshold: 0.85
            append_to_asset:
              quarterly_geo_contribution:
                quarter: "{row.quarter}"
                year: "{row.year}"
                geo_ounces: "{row.geo_ounces | parse_number}"
                revenue: "{row.revenue | parse_number}"

      output: "./data/enriched/{company.ticker}_assets_enriched.json"

    - name: extract_from_technical_reports
      for_each: technical_report in downloaded_technical_reports

      extraction_rules:
        section_14_resources:
          - heading_pattern: "14.*Mineral Resource"
          - extract_tables:
              classifications: [measured, indicated, inferred]
              fields: [tonnage, grade, contained_metal, units]
          - map_to_schema:
              resource_data:
                measured_resources: "{measured_table}"
                indicated_resources: "{indicated_table}"
                inferred_resources: "{inferred_table}"

        section_15_reserves:
          - heading_pattern: "15.*Mineral Reserve"
          - extract_tables:
              classifications: [proven, probable]
              fields: [tonnage, grade, contained_metal, units]
          - map_to_schema:
              reserve_data:
                proven_reserves: "{proven_table}"
                probable_reserves: "{probable_table}"

        section_22_production:
          - heading_pattern: "22.*Production|22.*Mine Production"
          - extract_tables:
              columns: [year, metal, production_quantity, units]
          - convert_to_annual_forecast: true
          - map_to_schema:
              production_forecast: "{production_table}"

        section_4_location:
          - heading_pattern: "4.*Property Description and Location"
          - extract_fields:
              - country
              - state_province
              - latitude
              - longitude
          - map_to_schema:
              geography: "{location_data}"

        metadata:
          - extract:
              - report_type: "NI_43-101"  # or JORC, SAMREC, PEA, PFS, FS
              - report_date
              - qualified_person
              - url: "{technical_report.source_url}"
          - map_to_schema:
              technical_report_references: "{metadata}"

      matching:
        - match_by: project_name
        - update_asset_record: true

      output: "./data/technical/{project_name}_technical_data.json"

# ==============================================================================
# STEP 3: CALCULATE NPV AND DISCOUNT RATES
# ==============================================================================
step_3_calculations:
  description: Calculate discount rates, NPV, and derived metrics

  tasks:
    - name: fetch_current_metal_prices
      actions:
        - if: metal_prices_source == "real_time_api"
          then:
            - api_call:
                endpoint: "https://api.metals.live/v1/spot"
                metals: [gold, silver, copper, platinum, palladium, zinc, lead, nickel]
            - save_to: "./data/metal_prices_current.json"
        - else:
            - use_static_prices:
                gold_per_oz: 3200
                silver_per_oz: 32
                copper_per_lb: 4.25
                platinum_per_oz: 1000
                palladium_per_oz: 1050
                zinc_per_lb: 1.20
                lead_per_lb: 0.95
                nickel_per_lb: 6.75
                cobalt_per_lb: 13.00

    - name: assign_jurisdiction_risk
      for_each: asset in all_assets
      rules:
        low_risk_countries: [Canada, Australia, USA, Chile, Botswana]
        moderate_risk_countries: [Mexico, Brazil, Peru, Argentina, Namibia, Ghana]
        high_risk_countries: [DRC, Tanzania, Mali, Burkina Faso, Zimbabwe, Venezuela]
        very_high_risk_countries: [countries with recent nationalization]

      calculate:
        jurisdiction_risk_rating:
          if: asset.geography.country in low_risk_countries
            then: "low"
          elif: asset.geography.country in moderate_risk_countries
            then: "moderate"
          elif: asset.geography.country in high_risk_countries
            then: "high"
          else: "very_high"

    - name: calculate_discount_rate
      for_each: asset in all_assets
      formula:
        base_rate:
          if: "gold" in asset.resource_type or "silver" in asset.resource_type
            then: 7.0  # precious metals base
          elif: "copper" in asset.resource_type or "zinc" in asset.resource_type
            then: 10.0  # base metals
          else: 8.0

        jurisdiction_premium:
          low: 0.0
          moderate: 2.0
          high: 5.0
          very_high: 7.0

        stage_premium:
          producing: 0.0
          development: 2.5
          advanced_exploration: 4.0
          exploration: 5.0
          care_and_maintenance: 3.0

        total_discount_rate:
          = base_rate + jurisdiction_premium[asset.jurisdiction_risk_rating] + stage_premium[asset.development_stage]

      output:
        discount_rate_components:
          base_rate_percent: "{base_rate}"
          jurisdiction_risk_premium_percent: "{jurisdiction_premium}"
          stage_risk_premium_percent: "{stage_premium}"
          total_discount_rate_percent: "{total_discount_rate}"

    - name: calculate_attributable_production
      for_each: asset in all_assets
      if: asset.production_forecast exists

      formula:
        for_each: year_data in asset.production_forecast
          if: asset.royalty_type == "stream"
            then:
              attributable = year_data.production_quantity * (asset.stream_rate.percentage_of_production / 100)
          elif: asset.royalty_type in ["NSR", "GRR", "GVR"]
            then:
              attributable = year_data.production_quantity * (asset.royalty_rate_percent / 100)
          elif: asset.royalty_type == "NPI"
            then:
              # Net profits interest - requires operator cost data
              attributable = (year_data.production_quantity * metal_price - estimated_costs) * (asset.royalty_rate_percent / 100)

          year_data.attributable_to_royalty = attributable

    - name: calculate_annual_cash_flows
      for_each: asset in all_assets
      if: asset.production_forecast exists

      formula:
        for_each: year_data in asset.production_forecast
          if: asset.royalty_type == "stream"
            then:
              revenue = year_data.attributable_to_royalty * metal_prices[year_data.metal]
              ongoing_payment = year_data.attributable_to_royalty * asset.stream_rate.ongoing_payment_per_unit
              net_cash_flow = revenue - ongoing_payment
          else:  # royalty
              revenue = year_data.attributable_to_royalty * metal_prices[year_data.metal]
              net_cash_flow = revenue

          pv_factor = 1 / ((1 + asset.discount_rate_components.total_discount_rate_percent / 100) ^ (year_data.year - current_year))
          discounted_cash_flow = net_cash_flow * pv_factor

          append_to:
            asset.cash_flow_projections:
              year: "{year_data.year}"
              revenue: "{revenue}"
              ongoing_stream_payments: "{ongoing_payment | null_if_not_stream}"
              net_cash_flow: "{net_cash_flow}"
              discounted_cash_flow: "{discounted_cash_flow}"

    - name: calculate_npv
      for_each: asset in all_assets
      if: asset.cash_flow_projections exists

      calculate:
        npv_at_asset_discount_rate:
          = sum(asset.cash_flow_projections.discounted_cash_flow)

        npv_5_percent:
          = recalculate_with_discount_rate(5.0)

        npv_8_percent:
          = recalculate_with_discount_rate(8.0)

        npv_10_percent:
          = recalculate_with_discount_rate(10.0)

      output:
        asset.npv_calculations:
          npv_5_percent: "{npv_5_percent}"
          npv_8_percent: "{npv_8_percent}"
          npv_10_percent: "{npv_10_percent}"
          npv_at_company_discount_rate: "{npv_at_asset_discount_rate}"
          calculation_date: "{current_date}"

# ==============================================================================
# STEP 4: VALIDATION AND QUALITY CHECKS
# ==============================================================================
step_4_validation:
  description: Validate data quality and schema compliance

  tasks:
    - name: schema_validation
      for_each: asset in all_assets
      validate_against: "./schemas/royalty_asset_schema.json"
      required_fields:
        - asset_id
        - company_name
        - company_ticker
        - project_name
        - operator_name
        - resource_type
        - development_stage
        - royalty_type

      on_validation_error:
        action: flag_for_manual_review
        log_to: "./data/validation_errors.json"

    - name: data_quality_checks
      for_each: asset in all_assets
      checks:
        - name: production_reserve_consistency
          rule: |
            if asset.mine_life_years and asset.production_forecast:
              forecast_duration = max(asset.production_forecast.year) - min(asset.production_forecast.year)
              assert abs(forecast_duration - asset.mine_life_years) <= 2, "Mine life doesn't match production forecast"

        - name: npv_relationship
          rule: |
            if asset.npv_calculations:
              assert asset.npv_calculations.npv_5_percent >= asset.npv_calculations.npv_10_percent, "NPV at lower discount rate should be higher"

        - name: discount_rate_sum
          rule: |
            if asset.discount_rate_components:
              calculated_sum = (asset.discount_rate_components.base_rate_percent + 
                              asset.discount_rate_components.jurisdiction_risk_premium_percent + 
                              asset.discount_rate_components.stage_risk_premium_percent)
              assert abs(calculated_sum - asset.discount_rate_components.total_discount_rate_percent) < 0.01, "Discount rate components don't sum correctly"

        - name: geo_revenue_consistency
          rule: |
            if asset.quarterly_geo_contribution:
              for quarter in asset.quarterly_geo_contribution:
                implied_price = quarter.revenue / quarter.geo_ounces if quarter.geo_ounces > 0 else 0
                assert 1500 <= implied_price <= 5000, f"Implied gold price {implied_price} seems unrealistic"

        - name: operator_ticker_valid
          rule: |
            if asset.operator_ticker:
              assert len(asset.operator_ticker) <= 6, "Ticker too long"
              assert asset.operator_ticker.isalnum() or '.' in asset.operator_ticker, "Invalid ticker format"

      on_check_failure:
        action: log_warning
        continue: true

# ==============================================================================
# STEP 5: OUTPUT GENERATION
# ==============================================================================
step_5_output:
  description: Generate final JSONL output file

  tasks:
    - name: enrich_metadata
      for_each: asset in all_assets
      add_fields:
        data_sources:
          asset_handbook_page: "{source_handbook_page}"
          md_and_a_references: "{source_mda_files}"
          technical_reports: "{source_technical_reports}"
          last_updated: "{current_timestamp}"

    - name: write_jsonl
      input: all_assets
      output_file: "./output/royalty_assets_{current_date}.jsonl"
      format: jsonl  # one JSON object per line
      sort_by: 
        - company_ticker
        - development_stage
        - npv_calculations.npv_at_company_discount_rate DESC

    - name: generate_summary_report
      create_file: "./output/scraping_summary_{current_date}.md"
      content: |
        # Royalty Asset Scraping Summary
        **Date:** {current_date}
        **Total Companies Processed:** {count(target_companies)}
        **Total Assets Extracted:** {count(all_assets)}

        ## Assets by Company
        {for_each company in target_companies:
          - {company.ticker}: {count(assets where company_ticker == company.ticker)} assets
        }

        ## Assets by Stage
        - Producing: {count(assets where development_stage == "producing")}
        - Development: {count(assets where development_stage == "development")}
        - Exploration: {count(assets where development_stage in ["exploration", "advanced_exploration"])}

        ## Assets by Royalty Type
        - NSR: {count(assets where royalty_type == "NSR")}
        - Stream: {count(assets where royalty_type == "stream")}
        - GRR/GVR: {count(assets where royalty_type in ["GRR", "GVR"])}
        - Other: {count(assets where royalty_type in ["NPI", "NPRI", "ORRI"])}

        ## Data Completeness
        - Assets with production forecast: {count(assets where production_forecast exists)} ({percentage}%)
        - Assets with reserve data: {count(assets where reserve_data exists)} ({percentage}%)
        - Assets with NPV calculated: {count(assets where npv_calculations exists)} ({percentage}%)
        - Assets with technical reports: {count(assets where technical_report_references exists)} ({percentage}%)

        ## Validation Issues
        - Total validation errors: {count(validation_errors)}
        - Assets flagged for review: {count(flagged_assets)}

    - name: create_csv_summary
      description: Create simplified CSV for quick analysis
      output_file: "./output/royalty_assets_summary_{current_date}.csv"
      columns:
        - asset_id
        - company_ticker
        - project_name
        - operator_name
        - country: geography.country
        - resource_type: join(resource_type, ",")
        - development_stage
        - royalty_type
        - royalty_rate_percent
        - mine_life_years
        - npv_8_percent: npv_calculations.npv_8_percent
        - jurisdiction_risk_rating
        - last_updated: data_sources.last_updated

# ==============================================================================
# ERROR HANDLING AND LOGGING
# ==============================================================================
error_handling:
  on_document_download_failure:
    - log_error: true
    - retry_count: 3
    - fallback: manual_download_required

  on_parsing_error:
    - log_error: true
    - save_problematic_section: "./data/errors/parse_errors/"
    - continue: true

  on_calculation_error:
    - log_error: true
    - set_field_to_null: true
    - flag_asset: needs_manual_calculation

  logging:
    log_file: "./logs/scraper_{current_date}.log"
    log_level: INFO
    log_format: "{timestamp} - {level} - {message}"

# ==============================================================================
# EXECUTION
# ==============================================================================
execution:
  mode: sequential  # or parallel
  continue_on_error: true
  save_intermediate_results: true
  checkpoint_after_each_step: true
---
# NPV CALCULATION FORMULAS FOR ROYALTY/STREAMING COMPANIES FORMULA LIBRARY

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
