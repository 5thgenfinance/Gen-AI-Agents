# GoldStockData Extraction Agent Instructions

## Mission
Extract company data from GoldStockData.com and encode it into JSONL format following the specified schema for AI/RAG ingestion.

---

## Authentication & Access

**Login Credentials:**
- URL: `https://www.goldstockdata.com/login`
- Username: `maxwere@icloud.com`
- Password: `5m4Q#8%7bqvC`

**Primary Data Source:**
- Navigate to "My Watchlist" or use company search/screener
- Extract all available company data systematically

---

## Output Schema (JSONL Format)

Each company record must be a single-line JSON object with the following structure:

### Required Fields

```json
{
  "id": "string (lowercase company name with underscores)",
  "company_name": "string (full company name)",
  "ticker_primary": "string (format: EXCHANGE:SYMBOL)",
  "ticker_secondary": "string or null (format: EXCHANGE:SYMBOL)",
  "category": "string (e.g., Mid-tier Producer, Junior Explorer)",
  "last_updated": "string (YYYY-MM-DD format)",
  
  "gold_reserves": {
    "total_proven_probable_oz": number or null,
    "total_measured_indicated_oz": number or null,
    "total_inferred_oz": number or null,
    "total_reserves_resources_oz": number or null,
    "plausible_proven_probable_oz": number or null,
    "plausible_measured_indicated_oz": number or null,
    "plausible_inferred_oz": number or null,
    "plausible_reserves_resources_oz": number or null
  },
  
  "gold_production_current": {
    "annual_production_oz": number or null,
    "cash_cost_per_oz_usd": number or null,
    "extra_operating_cost_per_oz_usd": number or null,
    "total_cost_per_oz_usd": number or null
  },
  
  "silver_reserves": {
    "total_proven_probable_oz": number or null,
    "total_measured_indicated_oz": number or null,
    "total_inferred_oz": number or null,
    "total_reserves_resources_oz": number or null,
    "plausible_proven_probable_oz": number or null,
    "plausible_measured_indicated_oz": number or null,
    "plausible_inferred_oz": number or null,
    "plausible_reserves_resources_oz": number or null
  },
  
  "silver_production_current": {
    "annual_production_oz": number or null,
    "cash_cost_per_oz_usd": number or null,
    "extra_operating_cost_per_oz_usd": number or null,
    "total_cost_per_oz_usd": number or null
  },
  
  "summary_notes": "string (full analyst commentary from Stock Summary section)",
  
  "metadata": {
    "source": "goldstockdata.com",
    "source_url": "string (full URL to company page)",
    "extraction_date": "string (YYYY-MM-DD format)",
    "data_quality": "string (verified|estimated|guess)"
  },
  
  "embedding_text": "string (natural language summary for vector embeddings)"
}
```

---

## Data Extraction Mapping

### 1. Company Identification
- **id**: Convert company name to lowercase, replace spaces with underscores (e.g., "Aris Mining Corp" → "aris_mining_corp")
- **company_name**: Extract from page header/title
- **ticker_primary**: Primary trading symbol (format: "TSX:ARIS")
- **ticker_secondary**: Secondary trading symbol if available (format: "NYSE:ARMN")
- **category**: Extract from "Category" field (e.g., "Mid-tier Producer")
- **last_updated**: Extract from "Last Analysis Data" date, convert to YYYY-MM-DD

### 2. Gold Reserves (from "Resource Data - GOLD" section)

**TOTAL section:**
- `total_proven_probable_oz`: "Proven & Probable" value (convert "M" to millions: 5.00M → 5000000)
- `total_measured_indicated_oz`: "Measured & Indicated" value
- `total_inferred_oz`: "Inferred" value
- `total_reserves_resources_oz`: "Reserves & Resources" value

**PLAUSIBLE section:**
- `plausible_proven_probable_oz`: "Proven & Probable" value under PLAUSIBLE
- `plausible_measured_indicated_oz`: "Measured & Indicated" value under PLAUSIBLE
- `plausible_inferred_oz`: "Inferred" value under PLAUSIBLE
- `plausible_reserves_resources_oz`: "Reserves & Resources" value under PLAUSIBLE

### 3. Gold Production Current (from "Resource Data - GOLD - CURRENT" section)
- `annual_production_oz`: Extract from "Annual Production" (e.g., "300,000oz." → 300000)
- `cash_cost_per_oz_usd`: Extract from "Cash Cost" (e.g., "$1,500" → 1500)
- `extra_operating_cost_per_oz_usd`: Extract from "Extra Operating Cost" (e.g., "$750" → 750)
- `total_cost_per_oz_usd`: Extract from "Total" (e.g., "$2,250" → 2250)

### 4. Silver Reserves (from "Resource Data - SILVER" section)
Follow same mapping as gold reserves. If data shows "n/a", set to `null`.

### 5. Silver Production Current (from "Resource Data - SILVER - CURRENT" section)
Follow same mapping as gold production. If data shows "n/a", set to `null`.

### 6. Summary Notes
- Extract complete text from "Stock Summary" or "Your Summary" section
- Preserve all text including dates, updates, and analyst commentary
- Clean up excessive whitespace but maintain paragraph structure
- Escape special JSON characters (quotes, backslashes)

### 7. Metadata
- `source`: Always "goldstockdata.com"
- `source_url`: Full URL of company page (e.g., "https://www.goldstockdata.com/company/222-Aris-Mining-Corp.html")
- `extraction_date`: Current date in YYYY-MM-DD format
- `data_quality`: Determine from source data:
  - "verified" if values don't have "(guess)" marker
  - "estimated" if some values marked as estimates
  - "guess" if marked as "(guess)" in source

### 8. Embedding Text
Generate natural language summary combining key data points:

**Template:**
```
[Company Name] ([Primary Ticker], [Secondary Ticker if exists]) is a [category] with plausible reserves of [proven_probable]M oz proven and probable, [measured_indicated]M oz measured and indicated, and [inferred]M oz inferred, totaling [total_reserves]M oz gold reserves and resources. Current annual gold production is [production] oz with cash costs of $[cash_cost] per oz, extra operating costs of $[extra_cost] per oz, and total production costs of $[total_cost] per oz. [Brief highlights from summary_notes - 1-2 key sentences]. Last updated: [last_updated in Month Day, Year format].
```

**Example:**
```
Aris Mining Corp (TSX:ARIS, NYSE:ARMN) is a Mid-tier Producer with plausible reserves of 4.5 million oz proven and probable, 13.86 million oz measured and indicated, and 3.6 million oz inferred, totaling 17.46 million oz gold reserves and resources. Current annual gold production is 300,000 oz with cash costs of $1,500 per oz, extra operating costs of $750 per oz, and total production costs of $2,250 per oz. The company operates primarily in Colombia with expansion plans at Segovia and Marmato, targeting 700K oz annual production if Toroparu and Soto Norte projects advance. Last updated: October 8, 2024.
```

---

## Data Cleaning Rules

### Number Formatting
- Convert all "M" (millions) to actual numbers: `5.00M` → `5000000`
- Convert all "K" (thousands) to actual numbers: `300K` → `300000`
- Remove currency symbols: `$1,500` → `1500`
- Remove commas from numbers: `1,500` → `1500`
- Convert percentages to decimals only if storing as ratio: `15%` → `0.15` (or keep as 15 if percentage)

### Text Formatting
- Escape double quotes: `"` → `\"`
- Escape backslashes: `\` → `\\`
- Remove excessive whitespace and line breaks
- Preserve intentional paragraph breaks in summary_notes
- Trim leading/trailing spaces

### Null Handling
- Use `null` (not "n/a", "N/A", "", or 0) for missing data
- Never use placeholder values like -1 or 999999
- If entire section is missing (e.g., all silver data), set all fields to `null`

### Date Formatting
- Always use YYYY-MM-DD format
- Convert from source format (MM/DD/YYYY) if needed
- Example: "10/08/2024" → "2024-10-08"

---

## Extraction Workflow

### Step 1: Login & Navigation
1. Navigate to https://www.goldstockdata.com/login
2. Enter credentials
3. Navigate to "My Watchlist" or company screener

### Step 2: Iterate Through Companies
For each company in the watchlist:
1. Click company link to open detail page
2. Wait for page to fully load
3. Extract all data fields per schema mapping

### Step 3: Data Validation
Before writing to JSONL:
- Verify all required fields are present
- Check number conversions are correct
- Validate date formats
- Ensure JSON is properly escaped
- Confirm no syntax errors

### Step 4: JSONL Output
- Write each company as a single line of JSON
- No pretty printing (single line per record)
- No trailing commas
- Append to output file: `goldstockdata_companies.jsonl`

### Step 5: Error Handling
- Log any extraction errors with company name and field
- Skip companies with critical missing data (company name, ticker)
- Continue processing remaining companies
- Generate error summary at end

---

## Output File Format

**Filename:** `goldstockdata_companies.jsonl`

**Format:** One JSON object per line, no line breaks within objects

**Example:**
```jsonl
{"id":"aris_mining_corp","company_name":"Aris Mining Corp","ticker_primary":"TSX:ARIS","ticker_secondary":"NYSE:ARMN","category":"Mid-tier Producer","last_updated":"2024-10-08","gold_reserves":{"total_proven_probable_oz":5000000,"total_measured_indicated_oz":18000000,"total_inferred_oz":8000000,"total_reserves_resources_oz":26000000,"plausible_proven_probable_oz":4500000,"plausible_measured_indicated_oz":13860000,"plausible_inferred_oz":3600000,"plausible_reserves_resources_oz":17460000},"gold_production_current":{"annual_production_oz":300000,"cash_cost_per_oz_usd":1500,"extra_operating_cost_per_oz_usd":750,"total_cost_per_oz_usd":2250},"silver_reserves":{"total_proven_probable_oz":null,"total_measured_indicated_oz":null,"total_inferred_oz":null,"total_reserves_resources_oz":null,"plausible_proven_probable_oz":null,"plausible_measured_indicated_oz":null,"plausible_inferred_oz":null,"plausible_reserves_resources_oz":null},"silver_production_current":{"annual_production_oz":null,"cash_cost_per_oz_usd":null,"extra_operating_cost_per_oz_usd":null,"total_cost_per_oz_usd":null},"summary_notes":"Aris Mining (previously GCM Mining and Gran Colombia Gold) is a mid-tier producer in Colombia. In 2022, they acquired Aris Mining in a merger of equals and took their name. They now have 30 million oz of resources and plan to expand production to around 700K oz (if they can build Toroparu and Sorte Norte). In 2025, they will produce around 275K oz (250K at Segovia and 25K at Marmato).","metadata":{"source":"goldstockdata.com","source_url":"https://www.goldstockdata.com/company/222-Aris-Mining-Corp.html","extraction_date":"2025-10-20","data_quality":"verified"},"embedding_text":"Aris Mining Corp (TSX:ARIS, NYSE:ARMN) is a Mid-tier Producer with plausible reserves of 4.5 million oz proven and probable, 13.86 million oz measured and indicated, and 3.6 million oz inferred, totaling 17.46 million oz gold reserves and resources. Current annual gold production is 300,000 oz with cash costs of $1,500 per oz, extra operating costs of $750 per oz, and total production costs of $2,250 per oz. The company operates primarily in Colombia with expansion plans at Segovia and Marmato. Last updated: October 8, 2024."}
{"id":"barrick_gold_corp","company_name":"Barrick Gold Corp","ticker_primary":"TSX:ABX","ticker_secondary":"NYSE:GOLD","category":"Major Producer","last_updated":"2024-10-15","gold_reserves":{"total_proven_probable_oz":68000000,"total_measured_indicated_oz":95000000,"total_inferred_oz":45000000,"total_reserves_resources_oz":140000000,"plausible_proven_probable_oz":65000000,"plausible_measured_indicated_oz":88000000,"plausible_inferred_oz":40000000,"plausible_reserves_resources_oz":128000000},"gold_production_current":{"annual_production_oz":4200000,"cash_cost_per_oz_usd":750,"extra_operating_cost_per_oz_usd":350,"total_cost_per_oz_usd":1100},"silver_reserves":{"total_proven_probable_oz":null,"total_measured_indicated_oz":null,"total_inferred_oz":null,"total_reserves_resources_oz":null,"plausible_proven_probable_oz":null,"plausible_measured_indicated_oz":null,"plausible_inferred_oz":null,"plausible_reserves_resources_oz":null},"silver_production_current":{"annual_production_oz":null,"cash_cost_per_oz_usd":null,"extra_operating_cost_per_oz_usd":null,"total_cost_per_oz_usd":null},"summary_notes":"Barrick Gold Corp is a major gold producer with operations across multiple continents.","metadata":{"source":"goldstockdata.com","source_url":"https://www.goldstockdata.com/company/123-Barrick-Gold-Corp.html","extraction_date":"2025-10-20","data_quality":"verified"},"embedding_text":"Barrick Gold Corp (TSX:ABX, NYSE:GOLD) is a Major Producer with plausible reserves of 65 million oz proven and probable, 88 million oz measured and indicated, and 40 million oz inferred, totaling 128 million oz gold reserves and resources. Current annual gold production is 4,200,000 oz with cash costs of $750 per oz, extra operating costs of $350 per oz, and total production costs of $1,100 per oz. Last updated: October 15, 2024."}
```

---

## Quality Assurance Checklist

Before finalizing extraction:

- [ ] All company records are on single lines (valid JSONL)
- [ ] No syntax errors (validate with JSON linter)
- [ ] All numbers properly converted (no "M" or "K" suffixes remaining)
- [ ] All dates in YYYY-MM-DD format
- [ ] Null values used for missing data (not empty strings or zeros)
- [ ] Special characters properly escaped in text fields
- [ ] All required fields present in every record
- [ ] Source URLs are complete and accessible
- [ ] Embedding text is natural language and informative
- [ ] File is UTF-8 encoded

---

## Post-Extraction Summary

Generate a summary report after extraction:

```
Extraction Summary
==================
Date: [YYYY-MM-DD HH:MM:SS]
Total Companies Processed: [number]
Successful Extractions: [number]
Failed Extractions: [number]
Output File: goldstockdata_companies.jsonl
File Size: [size in MB]

Data Quality Distribution:
- Verified: [count]
- Estimated: [count]
- Guess: [count]

Category Distribution:
- Major Producer: [count]
- Mid-tier Producer: [count]
- Junior Explorer: [count]
- [other categories]: [count]

Errors Encountered:
[List any errors with company names and fields]
```

---

## Usage Instructions for AI Agent

When executing this extraction:

1. **Read this entire document** before starting
2. **Follow the schema exactly** - do not add or remove fields
3. **Validate data types** - numbers must be numbers, not strings
4. **Use null for missing data** - never use placeholders
5. **Generate meaningful embedding_text** - this is critical for RAG performance
6. **Write valid JSONL** - one object per line, no exceptions
7. **Log all errors** but continue processing
8. **Verify output** with JSON validator before declaring success

---

## Example Execution Command

If using Python automation:

```python
python extract_goldstockdata.py --output goldstockdata_companies.jsonl --validate --summary
```

If using browser automation agent:

```
Navigate to goldstockdata.com, login with provided credentials, extract all watchlist companies following the JSONL schema, validate output, and generate summary report.
```

---

## Contact & Support

For schema questions or extraction issues, refer to this document as the source of truth. All field mappings and formatting rules are definitive and must be followed precisely for successful RAG ingestion.