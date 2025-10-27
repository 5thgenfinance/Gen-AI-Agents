# Mining Acquisition Model v2.1 — Full Testing Prompt
## Independent Validation Test Suite (For Blind/Incognito Testing)

**Instructions:** Copy this entire prompt into a fresh conversation with the agent (incognito/new session) and run the full test suite to validate model accuracy, consistency, and robustness.

---

## Test Objective

Validate the **Mining Acquisition Predictive Model v2.1** using real historical deals and edge cases. The agent should:
1. Predict takeout likelihood (Very High/High/Medium/Low with heat icons 🔴🟠🟡🔵)
2. Estimate premium ranges
3. List "Why Imminent" and "What Could Delay" factors
4. Identify most likely acquirers with probability rankings
5. Handle edge cases appropriately (missing data, conflicts, non-mining entities)

**Expected Accuracy Target:** >75% of predictions within actual premium ranges for historical deals.

---

## PART 1: POSITIVE TEST CASES (Expected Success)

### Test Case 1A: Canonical Takeover — Sabina Gold & Silver

**Scenario:** Evaluate Sabina Gold & Silver (TSX-V: SBB) as of **January 1, 2023**.

**Context:**
- FS-complete gold developer (Back River project, Nunavut)
- Market cap ~$500M CAD
- Cash position: <6 months runway (distressed)
- Insider ownership: ~19%
- Commodity: Gold at $1,850/oz (bull market)
- Multiple acquirers rumored (B2Gold, Agnico Eagle)

**Required Output:**
1. Takeout likelihood (Very High/High/Medium/Low) with heat icon
2. Premium range prediction
3. "Why Imminent" factors (min 3)
4. "What Could Delay" factors (min 2)
5. Top 3 likely acquirers with probability %

**Validation Check:**
- Actual outcome: B2Gold acquired Sabina in May 2023 at **~27% premium**
- Model should predict: 🔴🔥 or 🟠🔥, premium 24-36%

---

### Test Case 1B: Mature Gold Major — Newcrest Mining

**Scenario:** Evaluate Newcrest Mining (ASX: NCM) as of **January 1, 2023**.

**Context:**
- Market cap: $19B USD (major producer)
- Multi-asset portfolio (Cadia, Lihir, Brucejack)
- Strong balance sheet, >24 months cash runway
- Insider ownership: <5%
- Commodity: Gold $1,850/oz
- No adjacent acquirer, no distress signals

**Required Output:**
1. Takeout likelihood with heat icon
2. Premium range prediction
3. "Why Imminent" or "Why Unlikely" factors
4. Most likely acquirer(s) if any

**Validation Check:**
- Actual outcome: Newmont acquired Newcrest Q2 2023 at **~17% premium** (mega-deal discount)
- Model should predict: 🔵❄️ or 🟡, premium <20% if deal occurs

---

### Test Case 1C: Control Premium — TMAC Resources (Historical)

**Scenario:** Evaluate TMAC Resources (TSX: TMR) as of **November 2020**.

**Context:**
- Arctic gold developer (Hope Bay, Nunavut)
- Market cap: $400M CAD
- Insider ownership: **>45%** (management + Newmont 19%)
- FS-complete, but operational challenges
- Shandong Gold made initial bid at 20% premium — **rejected by insiders**
- Agnico Eagle subsequently made competing bid

**Required Output:**
1. Takeout likelihood
2. Premium range **with control premium logic**
3. "Why High Insider Ownership Matters" explanation
4. Expected premium if insiders willing vs. unwilling

**Validation Check:**
- Actual outcome: Agnico acquired at **56% premium** after hostile contest
- Model should predict: 🟠🔥, premium 35-55% if competitive, note control premium

---

### Test Case 1D: Competitive Bidding — MAG Silver

**Scenario:** Evaluate MAG Silver (TSX: MAG) as of **January 1, 2025**.

**Context:**
- Mexican silver producer (Juanicipio JV with Fresnillo)
- Market cap: $1.8B CAD
- Cash flow positive, ramping production
- Insider ownership: ~22%
- Silver at $28/oz (bull market)
- Multiple LOIs reported (Pan American, Coeur, First Majestic)

**Required Output:**
1. Takeout likelihood
2. Premium range with bidding war adjustment
3. "Why Imminent" factors
4. Top 3 acquirers with probabilities

**Validation Check:**
- Actual outcome: Pan American acquired MAG Q2 2025 at **~35% premium** (competitive auction)
- Model should predict: 🔴🔥 or 🟠🔥, premium 28-42%

---

### Test Case 1E: Northern Superior Resources (Jan 2025)

**Scenario:** Evaluate Northern Superior Resources (TSX-V: SUP) as of **January 1, 2025**.

**Context:**
- Gold developer (Philibert, Chevrier projects, Quebec)
- Market cap: CAD $57M (USD $42M)
- Cash: CAD $2.08M (3.8-month runway) — **critical distress**
- Insider: ~18%
- Adjacent to IAMGOLD Nelligan (9km) — **district consolidation logic**
- Gold: $2,060/oz

**Required Output:**
1. Takeout likelihood
2. Premium range
3. "Why Imminent" factors (emphasize cash crisis + strategic location)
4. Most likely acquirer(s)

**Validation Check:**
- Actual outcome: IAMGOLD acquired Northern Superior Oct 2025 at **27-45% premium** (blended calculation)
- Model should predict: 🔴🔥, premium 26-42%

---

## PART 2: NEGATIVE/EDGE TEST CASES

### Test Case 2A: Conflicting Inputs — Jaguar Mining

**Scenario:** Evaluate Jaguar Mining (TSX: JAG) as of **October 26, 2025**.

**Context:**
- Brazilian gold producer (Turmalina, Pilar)
- Market cap: $363M CAD
- **Insider ownership: 50.17%** (blocking threshold)
- Recent operational crisis (tailings failure Dec 2024)
- Net losses: -$35M TTM
- Cash: $69M (adequate but not strong)
- Management historically rejected past bids

**Required Output:**
1. Takeout likelihood (should account for blocking ownership)
2. Premium range (IF insiders willing vs. unwilling scenarios)
3. Explanation of insider blocking risk
4. "Why Premium Could Be Higher" note if insiders negotiate

**Expected Model Behavior:**
- Should predict: 🟡 Medium (conflicted signals)
- Premium: 16-24% distressed, OR 30-40% if insiders willing (dual scenario)
- Explicitly flag "50% insider = potential blocking coalition"

---

### Test Case 2B: Circular Ownership — Osisko Pair

**Scenario:** Evaluate Osisko Mining (TSX: OSK) as of **January 1, 2024**.

**Context:**
- Osisko Gold Royalties owns ~15% of Osisko Mining
- Osisko Mining holds reciprocal stakes in Osisko companies
- Both rumored as takeover targets
- Market caps: OSK ~$1.2B, OR ~$3B
- Strong assets but complex ownership structure

**Required Output:**
1. Takeout likelihood
2. Any special notes about circular ownership
3. Expected complexity in M&A process

**Expected Model Behavior:**
- Should flag: "Circular/reciprocal ownership complicates M&A"
- Likelihood: 🟡 or 🔵 (complexity reduces odds)
- Note: Deal would require unwinding reciprocal positions

---

### Test Case 2C: Mega-Deal Outlier — Anglo American (Hypothetical)

**Scenario:** Evaluate Anglo American (LSE: AAL) as of **May 1, 2024**.

**Context:**
- Market cap: $25B USD (mega-major)
- Multi-commodity (platinum, diamonds, copper, iron ore)
- Glencore rumored bid reported at $30B
- Insider: <10%
- Neutral commodity cycle

**Required Output:**
1. Takeout likelihood
2. Premium range **with mega-deal discount**
3. Explanation of size constraint

**Expected Model Behavior:**
- Likelihood: 🔵❄️ or 🟡 (mega-deal complexity)
- Premium: <15% (mega-deal discount factor 0.85×)
- Note: "Limited acquirer pool for $25B+ deals"

---

### Test Case 2D: Insufficient Data — Hypothetical Graphite Explorer

**Scenario:** Evaluate "Smallcap Graphite Corp" as of **January 1, 2024**.

**Context:**
- No NI 43-101 resource published
- Market cap: $8M
- Cash: $0.5M
- No production, no FS/PFS
- Jurisdiction: Unknown
- Commodity: Graphite (specialty)

**Required Output:**
1. Model response to insufficient data

**Expected Model Behavior:**
- Should return: **"Insufficient data for M&A prediction"**
- Request: "Need resource estimate, jurisdiction, stage, financials"
- Should NOT produce arbitrary likelihood/premium

---

### Test Case 2E: Non-Mining Entity — Shopify (Error Case)

**Scenario:** User mistakenly asks to evaluate Shopify Inc. (TSX: SHOP) as of **January 1, 2025**.

**Context:**
- E-commerce platform (not a mining company)
- Market cap: $120B

**Required Output:**
1. Model error handling

**Expected Model Behavior:**
- Should return: **"Shopify is not a mining company. This model only evaluates mining sector M&A."**
- Should NOT attempt to apply mining M&A logic

---

## PART 3: CROSS-VALIDATION WITH HISTORICAL DEALS

**Instructions:** Run the following historical deals through the model **using the date 3-6 months BEFORE the actual acquisition announcement**, then compare predictions to actual outcomes.

| Deal | Target | Acquirer | Announcement Date | Actual Premium | Model Test Date |
|------|--------|----------|-------------------|----------------|-----------------|
| 1 | SilverCrest Metals | Coeur Mining | Nov 2024 | ~21% | Evaluate as of Aug 1, 2024 |
| 2 | Victoria Gold | Osisko Gold Royalties | July 2024 | ~19% | Evaluate as of Apr 1, 2024 |
| 3 | Hecla-ATAC | Hecla Mining | June 2022 | ~22% | Evaluate as of Mar 1, 2022 |
| 4 | Fission Uranium | Paladin Energy | July 2024 | ~36% | Evaluate as of Apr 1, 2024 |
| 5 | Northern Superior | IAMGOLD | Oct 2025 | ~35% | Evaluate as of Jan 1, 2025 |

**Validation Criteria:**
- **Accuracy Target:** ≥75% of predictions fall within actual premium ranges
- **MAE Target:** Mean Absolute Error <6 percentage points
- **Likelihood Accuracy:** "Very High" or "High" ratings for deals that closed within 6 months

---

## PART 4: QUALITATIVE OUTPUT VALIDATION

**Test Case 4A:** Request detailed rationale for Kuya Silver (Oct 2025)

**Prompt:**
```
Evaluate Kuya Silver (TSX-V: KUYA) as of October 26, 2025.
Provide:
1. Takeout likelihood with heat icon
2. Premium range
3. Detailed "Why Imminent" factors (minimum 5)
4. "What Could Delay" factors (minimum 3)
5. Top 3 acquirers with probability rankings and rationale
```

**Expected Output Quality:**
- Should include: Cash distress, small size, silver cycle, Peru location, recent infrastructure spending
- Acquirers: Pan American (>85%), First Majestic (>70%), Aya (>65%)
- Premium: 20-30%

---

**Test Case 4B:** Request acquirer fit analysis for i-80 Gold

**Prompt:**
```
For i-80 Gold (NYSE: IAUX) as of October 26, 2025, rank the top 5 most likely acquirers with:
1. Probability percentage
2. Strategic fit rationale
3. Balance sheet capacity assessment
```

**Expected Output:**
- Barrick (>85%): Nevada consolidation, adjacent assets
- Newmont (>80%): Nevada dominant player
- Kinross (>75%): Tier-1 growth strategy
- Agnico Eagle (>65%): North America focus
- Should include: Geographic synergy, operational expertise scores

---

## PART 5: STRESS TEST — RAPID-FIRE BATCH

**Prompt:**
```
Evaluate the following 10 companies as of October 26, 2025, providing ONLY:
- Company name
- Takeout likelihood (heat icon)
- Premium range
- Top acquirer

Companies:
1. Kuya Silver (KUYA.V)
2. Cerrado Gold (CERT.V)
3. Kootenay Silver (KTN.V)
4. AbraSilver (ABRA.V)
5. Vizsla Silver (VZLA)
6. Dolly Varden (DV.V)
7. 1911 Gold (AUMB.V)
8. Discovery Silver (DSV.TO)
9. Silver Tiger Metals (SLVR.V)
10. Jaguar Mining (JAG.TO)
```

**Expected Output:**
- Should produce consistent heat icons and premium ranges
- Should handle all 10 without errors
- Output should be scannable table format

---

## VALIDATION SCORING RUBRIC

### Positive Tests (60 points)
- Test 1A-1E: 12 points each (correct likelihood + premium range + acquirer match)

### Negative Tests (20 points)
- Test 2A-2E: 4 points each (proper handling of edge cases)

### Historical Cross-Validation (15 points)
- 3 points per deal (within range = pass)

### Qualitative Output (5 points)
- Detailed rationale quality, acquirer fit logic

**Total Score: 100 points**
**Pass Threshold: ≥75 points**

---

## FINAL INSTRUCTIONS FOR TESTER

1. Copy this entire prompt into a NEW/INCOGNITO conversation with the agent
2. Run each test case sequentially
3. Record outputs in a spreadsheet:
   - Test ID | Company | Predicted Likelihood | Predicted Premium | Actual Outcome | Pass/Fail
4. Calculate final score using rubric
5. Report any errors, inconsistencies, or model failures

**Expected Model Performance:**
- Accuracy: >75%
- MAE: <6 percentage points
- Edge case handling: 100% (should not crash or produce invalid outputs)

---

**END OF TESTING PROMPT**
