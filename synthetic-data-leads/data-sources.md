# Data Sources

The lead-gen agent operates on data that, in production, would be assembled from publicly available sources plus KUKA's internal CRM. For this Step-4 demo, the same data is staged manually as files under `synthetic-data-leads/`. This document maps each demo file back to its real-world source so the demo's narrative is honest about where the inputs come from.

---

## Per-Prospect Data Packets — `prospects/<prospect-folder>/`

| File | Real-world source | What it represents |
|------|-------------------|---------------------|
| `company-financials.json` | [Bundesanzeiger](https://www.bundesanzeiger.de), [North Data](https://www.northdata.com), Creditreform, Dun & Bradstreet | Public financial filings: revenue, EBIT, headcount trend, ownership, industry code (NACE), founding year, registered address |
| `website-snapshot.md` | The company's own website (about / news / press pages), scraped on a periodic cadence; supplemented with trade press monitoring (Automationspraxis, Produktion, Maschinenmarkt) | Company self-description, recent announcements, hiring signals, capex and expansion news |
| `linkedin-stakeholders.json` | [LinkedIn Sales Navigator](https://www.linkedin.com/sales/), Apollo.io, Cognism — enriched with seniority and tenure | The list of identifiable employees with their titles — used to find the right automation decision-maker |
| `contact-details.json` | Company website "Kontakt/Impressum" page, [Gelbe Seiten](https://www.gelbeseiten.de), [Das Telefonbuch](https://www.dastelefonbuch.de), plus enrichment vendors (Lusha, Apollo) | Phone, email, postal address — both at the company level and (where available) per individual |

---

## Existing Customer Anchors — `kuka-customers/*.json`

| Field set | Real-world source |
|-----------|-------------------|
| Company-level facts (registration, industry, headcount, revenue) | KUKA CRM (Salesforce) — the customer master record |
| `key_stakeholders` | KUKA CRM contacts |
| `products_purchased` and `annual_revenue_to_kuka_eur` | KUKA CRM opportunity & contract records, plus billing system |
| `customer_profile_summary` | The account manager's notes. In future: auto-summarised from CRM activity history |

---

## Reference Documents

| File | Source / Owner |
|------|----------------|
| `kuka-offering.md` | KUKA Sales — the canonical product proposition document |
| `icp-and-scoring-rubric.md` | KUKA Sales Operations — the published ICP and scoring methodology used by the agent |

---

## Step 4 vs Step 6 — How These Sources Are Wired In

| | Step 4 (this demo) | Step 6 (integrated) |
|---|---|---|
| Financial data | Manually exported and pasted into `company-financials.json` | Live Creditreform / D&B API call per prospect |
| Website data | Hand-scraped into `website-snapshot.md` | Headless-browser scraper on a weekly cadence + trade press monitoring API |
| LinkedIn stakeholders | Manually copied into `linkedin-stakeholders.json` | LinkedIn Sales Navigator API + enrichment vendor (Apollo / Cognism) |
| Contact details | Manually pasted from company websites | Direct API integration with enrichment vendor |
| KUKA customer anchors | Hand-built JSON files | Live read from Salesforce + ERP |
| Output to account manager | Markdown files + static HTML app | Push directly into Salesforce as ranked Lead records, with cheat sheet attached |

The Step-4 demo deliberately uses static files so the reader can audit every input — and so the demo runs without any system integration. The agent's reasoning is identical in both architectures.
