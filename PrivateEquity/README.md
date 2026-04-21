# Liquidity, Counterparty, and Balance-Sheet Risk Toolkit

This repository describes three cooperating agents for insurance risk analysis:

- **AgentBB (Segments)** – analyzes NAIC Life (Blue Book) liabilities by product and withdrawal status to assess liquidity risk.
- **AgentS (Reinsurance / Schedule S)** – analyzes NAIC Schedule S reinsurance data to assess counterparty risk.
- **AgentA (Assets / 10-K)** – analyzes SEC 10-K investment disclosures to assess balance-sheet and investment risk.

Together, they help answer: **What if policyholders run? What if my reinsurer fails? What if my assets lose value?**

---

## AgentBB – Liquidity Risk (Liability Segments)

**Purpose:** Quantify liability balances by withdrawal status and product type to evaluate liquidity risk.

**Typical inputs:**
- Public ticker(s) and any mapping from ticker → NAIC life entities.
- Latest NAIC Life (Blue Book) extracts or vendor data containing:
  - Balance sheet liability lines.
  - Reserve/deposit exhibits by product.

**How to use AgentBB:**
- Ingest Blue Book data and map each legal entity to its public holding company.
- Segment liabilities into:
  - **Deposits in free-withdrawal status** – highest immediate liquidity risk.
  - **Deposits under surrender-charge period** – medium liquidity risk; still subject to charges or MVAs.
  - **Insurance benefit reserves** by product.
- Classify by product type:
  - Term life, permanent life, payout annuity, deferred annuity, other annuity, health.

**What to look for:**
- Large blocks of deferred annuities or deposit funds in free-withdrawal status.
- High proportions of lapse-sensitive products (e.g., deferred annuities) relative to more stable payout annuities.

**Example questions for AgentBB:**
- “For ticker X, show total deposits in free withdrawal vs under surrender charge, by product and legal entity.”
- “Highlight entities where free-withdrawal deposits exceed Y% of total liabilities.”

---

## AgentS – Counterparty Risk (Reinsurance / Schedule S)

**Purpose:** Understand how much risk is transferred to or assumed from specific reinsurance counterparties and which public companies stand behind them.

**Typical inputs:**
- Normalized NAIC Schedule S data (ceded and assumed) for the relevant year, including parts for funds-withheld/modco where applicable.
- NAIC company master or mapping table for reinsurance counterparties.

**How to use AgentS:**
- Normalize Schedule S parts and sections and reconcile totals to the statutory statement.
- Map each counterparty (cedant and reinsurer) to:
  - Ultimate parent legal entity.
  - CIK and stock ticker, where public.
- Produce exposures by:
  - Ceded vs assumed.
  - Line of business and treaty type.
  - Funds-withheld/modco structures.

**What to look for:**
- Concentration in the top 5–10 reinsurers by ceded reserves and recoverables.
- Large funds-withheld/modco balances where asset and counterparty risk are intertwined.
- Mix of public vs private counterparties and any known weaker credits (can be cross-checked via AgentA on those parents).

**Example questions for AgentS:**
- “For filer Y, show ceded reserves and recoverables by ultimate parent ticker, including funds-withheld/modco exposures.”
- “Identify counterparties where ceded balances exceed Z% of total policy liabilities.”

---

## AgentA – Balance-Sheet Risk (Assets / 10-K)

**Purpose:** Evaluate the risk profile of assets backing the liabilities and reinsured blocks, using SEC 10-K investment disclosures.

**Typical inputs:**
- Public company ticker or CIK.
- Latest 10-K (and, if needed, 10-Q) filings from EDGAR.

**How to use AgentA:**
- Retrieve the latest 10-K from EDGAR.
- Extract fixed-maturity and other invested asset tables, including security-level data where available:
  - Issuer, description.
  - CUSIP/ISIN/ticker (when disclosed).
  - Maturity, coupon, rating, sector, fair value.
- Reconcile asset totals to the balance sheet and risk disclosures.

**What to analyze:**
- **Credit risk:** ratings mix, sector concentrations (e.g., BBB corporate share, structured credit exposure).
- **Interest-rate risk:** duration, maturity ladders, and sensitivity disclosures.
- **Liquidity risk:** proportion of less-liquid investments (private credit, alternatives, real estate) vs liabilities segmented by AgentBB.

**Example questions for AgentA:**
- “For ticker X, summarize fixed-maturity portfolio by rating bucket, sector, and maturity band.”
- “Identify the 20 largest CUSIP-level positions by fair value and their sectors.”

---

## Integrated Use Cases

### Liquidity risk view

- Combine **AgentBB** outputs (liability segments) with **AgentA** asset-liquidity profiles.
- Assess whether liquid assets are sufficient to cover free-withdrawal deposits and near-term surrender risk under stress scenarios.

### Counterparty risk view

- Combine **AgentS** exposure tables with **AgentA** analysis of reinsurer parents that are publicly traded.
- Evaluate both the size of ceded balances and the financial strength/portfolio quality of key reinsurers.

### Balance-sheet risk view

- Use **AgentA** for asset-side risk, **AgentBB** for liability structure, and **AgentS** for risk transfer via reinsurance.
- Ensure each agent preserves identifiers (NAIC codes, tickers, CIKs, entity IDs, CUSIPs) so you can join the three data sets in your own analytics stack.
- Build scenario and stress tests that move consistently across assets, liabilities, and reinsurance programs (e.g., rate shocks, credit events, lapse waves).

---

## Implementation Notes

- Each agent has its own detailed workflow and data schema (`segments.json`, `reins.json`, `assets.json`).
- Start by wiring your data ingestion to those schemas, then connect the agents to your normalized tables.
- Treat statutory (NAIC) filings as the ground truth for legal-entity results, and SEC 10-K filings as the ground truth for consolidated public-company reporting.
