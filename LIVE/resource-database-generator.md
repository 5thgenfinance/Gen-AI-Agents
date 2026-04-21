# Uranium Project Inventory Schema Documentation

## Overview

This document describes the Uranium Company & Project Inventory Schema, a standardized JSON format for uranium mining company and project data. The schema is designed to support NI 43-101 (Canadian) and S-K 1300 (US SEC) compliant mineral resource inventory tracking.

**Schema Version:** 1.0  
**Last Updated:** January 29, 2026  
**Primary Use:** Uranium mining project database population and validation

---

## Schema Structure

The schema defines two primary entities:

### 1. Companies
Contains company-level information including stock structure and dilution metrics.

### 2. Projects
Contains project-level information including location, development stage, mining method, processing method, and mineral resources.

---

## Detailed Field Definitions

### Companies Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `company_id` | integer | Yes | Unique company identifier (primary key) |
| `ticker` | string | Yes | Stock ticker symbol (max 10 chars) |
| `company_name` | string | Yes | Legal company name (max 255 chars) |
| `exchange` | string | Yes | Exchange listing (e.g., NASDAQ/TSXV, ASX/OTCQX) (max 50 chars) |
| `shares_outstanding` | integer | Yes | Total shares outstanding (common shares only) |
| `options_warrants` | integer | Yes | Total dilutive securities (stock options + warrants combined) |
| `fully_diluted_shares` | integer | Yes | Fully diluted share count = shares_outstanding + options_warrants |
| `last_updated` | string (date) | Yes | Date of last share count update (YYYY-MM-DD format) |

**Notes on Companies:**
- `shares_outstanding` should reflect common shares only, excluding options and warrants
- `options_warrants` is the sum of all outstanding in-the-money options and warrants
- `fully_diluted_shares` is a calculated field: shares_outstanding + options_warrants
- Share counts should be sourced from most recent company filings (10-K, AIF, annual reports)

---

### Projects Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `project_id` | integer | Yes | Unique project identifier (primary key) |
| `project_name` | string | Yes | Project name (max 255 chars) |
| `project_state` | enum | Yes | Development stage: PRODUCTION, PRE_PROD, DEVELOPMENT, PRE_PFS, PRE_PEA |
| `ownership_percentage` | number | Yes | Company ownership % (0-100); for JVs, reflect company's stake |
| `production_start_date` | string (date) | No | Production start date (YYYY-MM-DD); null if not yet producing |
| `mine_life_years` | integer | Yes | Estimated mine life in years (from technical report) |
| `mining_method` | enum | Yes | ISR, OPEN_PIT, UNDERGROUND, HEAP_LEACH, or CONVENTIONAL_MILL |
| `processing_method` | enum | Yes | IX_PLANT, CONVENTIONAL_MILL, SOLVENT_EXTRACTION, HEAP_LEACH, or PRECIPITATION |
| `country` | string | Yes | Country location (max 100 chars) |
| `source_document` | string | Yes | NI 43-101 or S-K 1300 technical report title (max 500 chars) |
| `source_link` | string (URI) | Yes | Direct hyperlink to source technical report |
| `last_pea_date` | string (date) | No | Date of most recent PEA (Preliminary Economic Assessment) |
| `last_pfs_date` | string (date) | No | Date of most recent PFS (Prefeasibility Study) |
| `primary_mineral` | string | Yes | Primary mineral commodity (e.g., U3O8, Cu, Au, Mo) (max 20 chars) |
| `unit_of_mineral` | enum | Yes | Unit of measurement: lbs, kg, tonnes, oz, or g |
| `aisc_per_unit` | number | No | Cost in USD per unit of measurement to extract.  Typically this is estimated in PFS or PEA |
| `proven_probable_reserves` | number | No | Proven + Probable reserves; null if not separately reported |
| `measured_indicated_reserves` | number | No* | Measured + Indicated resources (*required unless no resources defined) |
| `inferred_reserves` | number | No | Inferred resources; NOT included in economic assessments |
| `last_updated` | string (date) | Yes | this date should reflect the age of the data represented, probably the as of date in PFS or PEA or on source document |

**Notes on Projects:**
- `project_state` options:
  - **PRODUCTION**: Project is operational and generating revenue
  - **PRE_PROD**: Project has final permits and is under construction
  - **DEVELOPMENT**: Project has preliminary permits; advancing toward feasibility study
  - **PRE_PFS**: Project has PEA but not yet PFS (prefeasibility study)
  - **PRE_PEA**: Project is in early exploration/development stage
  
- `mining_method` definitions:
  - **ISR**: In-Situ Recovery - solution mining via injection/extraction wells (primary uranium method)
  - **OPEN_PIT**: Surface mining with ore hauled to mill
  - **UNDERGROUND**: Underground mining; used for deeper deposits
  - **HEAP_LEACH**: Stockpile leaching; lower capex, lower recovery
  - **CONVENTIONAL_MILL**: Traditional milling of extracted ore

- `processing_method` definitions:
  - **IX_PLANT**: Ion Exchange plant (primary uranium recovery method)
  - **CONVENTIONAL_MILL**: Milling and concentration facility
  - **SOLVENT_EXTRACTION**: Solvent extraction recovery from pregnant solutions
  - **HEAP_LEACH**: Stacked leaching operation
  - **PRECIPITATION**: Precipitation-based recovery (e.g., magnesium diuranate)

- Resource Classifications (per NI 43-101):
  - **Measured Resources**: Highest confidence; detailed sampling/drilling
  - **Indicated Resources**: Good confidence; reasonable sampling density
  - **Measured + Indicated (M&I)**: Combined M+I; forms basis for economic studies
  - **Inferred Resources**: Lower confidence; limited sampling; NOT included in PEA/PFS economics
  - **Proven + Probable Reserves**: Historically used; less common in uranium (use M&I instead)

---

## Data Source Hierarchy & Instructions

### Primary Source Selection (Priority Order)

**1. SEDAR+ / SEDAR (Canadian Projects)**
- URL: https://www.sedarplus.ca
- Search company name or ticker
- Look for "NI 43-101 Technical Report" or "Technical Report Summary"
- Status: **Audited, regulatory-compliant source of truth**

**2. SEC EDGAR (US Projects)**
- URL: https://www.sec.gov/edgar
- Search company CIK or ticker
- Look for S-K 1300 Technical Report Summary filings
- Status: **Audited, regulatory-compliant source of truth**

**3. Company Website**
- Navigate to Investor Relations section
- Look for "Technical Reports" or "Projects" section
- Cross-reference with SEDAR+/SEC filings to verify date and content
- Status: **Audited if same as SEDAR+/SEC filing**

**4. Company News Releases**
- Press releases may announce updated estimates
- Only use if cross-referenced with formal technical report
- Status: **Use with caution; verify with formal filings**

**5. Best Estimate (Non-Audited)**
- Use only if NI 43-101 / S-K 1300 report does not exist
- Source from corporate presentations, investor decks, or management guidance
- **MUST DISCLOSE: `source_document` should indicate "(Non-Audited Estimate)" or "(Management Guidance)"`
- Example: `"source_document": "Corporate Presentation - Q4 2025 (Non-Audited Estimate)"`

---

## Agent Instructions for Population

### Overview
This agent is designed to accept a list of uranium mining companies and automatically populate the schema with accurate, sourced data.

### Input Format
Provide a comma-separated list or JSON array of company names or tickers:

```
Encore Energy, Kazatomprom, Boss Energy, Cameco
```

or

```json
[
  "Encore Energy",
  "Kazatomprom",
  "Boss Energy"
]
```

### Processing Steps

1. **Company Verification**
   - Verify company name, ticker, and exchange
   - Confirm current as of latest filing
   - Source from most recent 10-K, AIF, or annual report

2. **Share Count Research**
   - Search SEC EDGAR or SEDAR+ for latest capitalization table
   - Identify shares outstanding (common shares)
   - Identify all options and warrants (including strike price and expiry)
   - Calculate fully_diluted_shares
   - Record last_updated date

3. **Project Identification**
   - Search company website for full list of projects
   - For each project:
     - Confirm project name and location (country)
     - Determine current development stage
     - Identify company ownership % (handle JVs)
     - Note production start date if applicable

4. **Technical Data Research**
   - **Primary Search**: SEDAR+ or SEC EDGAR for NI 43-101 / S-K 1300 Technical Report
   - **Query Pattern**: "[Company Name] [Project Name] NI 43-101" or "[Company Name] [Project Name] S-K 1300"
   - If found:
     - Record source_document title
     - Record source_link (direct PDF URL if available)
     - Extract: mining_method, processing_method, mine_life_years
     - Extract: last_pea_date, last_pfs_date
     - Extract: measured_indicated_reserves, inferred_reserves
     - Extract: primary_mineral, unit_of_mineral
     - Status: **Audited - Use as final source**

   - **Secondary Search**: Company website Technical Reports section
     - Verify dates match SEDAR+/SEC filings
     - Cross-check reserve figures
     - Status: **Audited if matches SEDAR+/SEC**

   - **Fallback Search**: Company investor presentations, news releases
     - Only use if technical report not available
     - **MUST DISCLOSE** source as non-audited in source_document field
     - Example: `"(Management Guidance - No NI 43-101 Available)"`
     - Status: **Non-Audited Estimate**

5. **Validation & Quality Control**
   - Confirm all required fields are populated
   - Verify reserve figures are realistic (within industry norms)
   - Check that source_links are active and accessible
   - Verify dates are in YYYY-MM-DD format
   - Confirm mining_method and processing_method align logically
   - Flag any null values that should have data

6. **Output**
   - Generate valid JSON conforming to schema
   - Include metadata noting data freshness and confidence level
   - List all sources used for each company

---

## Disclosure Requirements

### Audited Data (SEDAR+ / SEC Filing)
```json
{
  "source_document": "NI 43-101 Technical Report Summary - Project Name",
  "source_link": "https://www.sedarplus.ca/.../document.pdf",
  "data_status": "Audited - NI 43-101 Compliant"
}
```

### Non-Audited Data (Company Guidance / Best Estimate)
```json
{
  "source_document": "Corporate Presentation Q4 2025 (Non-Audited Estimate)",
  "source_link": "https://company.com/investor/Q4-2025-Presentation.pdf",
  "data_status": "Non-Audited - Best Estimate Based on Management Guidance"
}
```

**Important Note**: Any field sourced from non-audited materials must be clearly flagged in the source_document field with one of the following labels:
- "(Non-Audited Estimate)"
- "(Management Guidance)"
- "(Corporate Presentation - Not NI 43-101)"
- "(Best Estimate - No Technical Report Available)"

---

## Common Field Combinations

### ISR Uranium Project (Standard Encore Energy Model)
```json
{
  "mining_method": "ISR",
  "processing_method": "IX_PLANT",
  "primary_mineral": "U3O8",
  "unit_of_mineral": "lbs"
}
```

### Open Pit Gold Project
```json
{
  "mining_method": "OPEN_PIT",
  "processing_method": "CONVENTIONAL_MILL",
  "primary_mineral": "Au",
  "unit_of_mineral": "oz"
}
```

### Underground Copper Project
```json
{
  "mining_method": "UNDERGROUND",
  "processing_method": "CONVENTIONAL_MILL",
  "primary_mineral": "Cu",
  "unit_of_mineral": "tonnes"
}
```

---

## Examples

### Example 1: Encore Energy - Alta Mesa (Audited)
```json
{
  "project_id": 1,
  "project_name": "Alta Mesa Uranium Project",
  "project_state": "PRODUCTION",
  "ownership_percentage": 70.0,
  "production_start_date": "2024-06-01",
  "mine_life_years": 15,
  "mining_method": "ISR",
  "processing_method": "IX_PLANT",
  "country": "USA",
  "source_document": "S-K 1300 Technical Report Summary - South Texas Integrated Uranium Projects",
  "source_link": "https://encoreuranium.com/wp-content/uploads/2025/03/South-Texas-Integrated-Uranium-Projects-SK-1300-NI-43-101.pdf",
  "last_pea_date": "2025-02-19",
  "last_pfs_date": null,
  "primary_mineral": "U3O8",
  "unit_of_mineral": "lbs",
  "aisc_per_unit": $44,
  "proven_probable_reserves": null,
  "measured_indicated_reserves": 3123000,
  "inferred_reserves": 3192000,
  "last_updated": "2025-12-31"
}
```

### Example 2: Hypothetical Project (Non-Audited)
```json
{
  "project_id": 99,
  "project_name": "Example Copper Project",
  "project_state": "DEVELOPMENT",
  "ownership_percentage": 100.0,
  "production_start_date": null,
  "mine_life_years": 12,
  "mining_method": "OPEN_PIT",
  "processing_method": "CONVENTIONAL_MILL",
  "country": "Peru",
  "source_document": "Corporate Presentation Q1 2026 (Non-Audited Estimate)",
  "source_link": "https://company.example.com/investor/Q1-2026.pdf",
  "last_pea_date": "2024-06-15",
  "last_pfs_date": null,
  "primary_mineral": "Cu",
  "unit_of_mineral": "tonnes",
  "aisc_per_unit": null,
  "proven_probable_reserves": null,
  "measured_indicated_reserves": 500000000,
  "inferred_reserves": 250000000,
  "last_updated": "2025-12-31"
}
```

---

## Troubleshooting & Common Issues

### Issue: Cannot Find NI 43-101 Report
- **Solution 1**: Search company's investor relations website directly
- **Solution 2**: Check SEC EDGAR if US-listed (may be filed as S-K 1300)
- **Solution 3**: Use best estimate from latest corporate presentation; disclose as "(Non-Audited)"

### Issue: Multiple Technical Reports (Different Dates)
- **Solution**: Use the MOST RECENT report date as `last_pea_date` or `last_pfs_date`
- Note previous reports in comments if needed

### Issue: Joint Venture Projects
- **Solution**: Record company's ownership % in `ownership_percentage` field
- Example: If company owns 30% of JV, use `ownership_percentage: 30.0`
- If reserves are reported on 100% basis, consider noting in separate metadata

### Issue: Reserve Units Inconsistent with Source
- **Solution**: Convert to standard units in `unit_of_mineral`
- Uranium: convert all to `lbs` (standard for market)
- Metals: convert to most common unit (Cu/Au in tonnes, Au can also be oz)
- Document conversion factor if non-standard

---

## Validation Checklist

Before submitting JSON data, verify:

- [ ] All required fields are populated
- [ ] Dates are in YYYY-MM-DD format
- [ ] Ownership_percentage is between 0-100
- [ ] Mining_method matches defined enum
- [ ] Processing_method matches defined enum
- [ ] Unit_of_mineral matches defined enum
- [ ] Source_link is a valid, accessible URL
- [ ] Non-audited sources are clearly flagged in source_document
- [ ] Mineral resources make logical sense (M&I ≥ Inferred in most cases)
- [ ] Project_state aligns with reserve size and development stage

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-01-29 | Initial release with company and project structures |

---

## Support & Contact

For schema questions, ambiguities, or to propose new fields:
- Review SEDAR+ or SEC filings for standard terminology
- Consult company technical reports for field value guidance
- Verify against existing populated examples

---

**Document Status**: Final  
**Last Review**: January 29, 2026  
**Next Review**: Q2 2026
