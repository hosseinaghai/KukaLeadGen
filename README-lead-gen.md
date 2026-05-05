# KUKA Lead-Generation Agent — Demo

## The Problem

Today, a KUKA account manager prospecting for new manufacturing customers spends roughly **2 hours per qualified lead**:

1. Pull Creditreform / North Data financials on a candidate company
2. Read the company website to understand what they manufacture and how
3. Trawl LinkedIn for the right person to call (VP Production? CTO? CEO?)
4. Hunt down a phone number on the company Impressum or Das Telefonbuch
5. Mentally compare the prospect to existing KUKA customers — who does this company "look like"? What worked there?
6. Draft a cold-call opener and pre-empt the first 2-3 objections

Multiplied across the sales team, this is hundreds of hours per year of pre-call research — most of which is generic pattern-matching that an agent can do in seconds. Worse, account managers frequently call the *wrong* person at the company (CEO when there's a VP Production, VP Production when the Plant Manager actually owns capex) and lose the meeting before it starts.

---

## The Solution (Step 4 — No Systems Integration Required)

A Cursor agent skill that reads:

- Public financials, website snapshots, LinkedIn-style stakeholder lists, and contact details for each prospect (staged manually as files for this Step-4 demo)
- 4 existing KUKA customer profiles as look-alike anchors
- The 3-pillar automation offering definition
- A published 7-factor ICP and scoring rubric

It outputs:

```
┌─────────────────────────────────────────────────────────┐
│                    MANUAL INPUTS                         │
│                                                          │
│  Public financials       (Creditreform-style JSON)       │
│  Website snapshots       (scraped about-us + news)       │
│  LinkedIn stakeholders   (Sales Nav-style enrichment)    │
│  Contact details         (Impressum / enrichment)        │
│  KUKA customer anchors   (4 existing customers)          │
│  ICP & scoring rubric    (published rubric)              │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│              CURSOR AGENT SKILL                          │
│                                                          │
│  Score → Match anchors → Identify stakeholder → Draft    │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│                   OUTPUTS                                │
│                                                          │
│  Prioritized prospect list   (ranked + rationale)        │
│  Prospect scorecards         (machine-readable JSON)     │
│  Stakeholder map             (right person to call)      │
│  Cold-call cheat sheets      (one per prospect)          │
│  Interactive HTML app        (clickable demo)            │
└─────────────────────────────────────────────────────────┘
```

**Time to generate:** Seconds, not 2 hours/lead.

The account manager opens the HTML app, sorts the prospect pipeline by score, picks the top 2-3 to call this week, reviews the cheat sheet, and dials. No systems integration needed.

---

## Step 4 vs. Step 6 — What Changes with Integration

| Aspect | Step 4 (Today's Demo) | Step 6 (Future — Integrated) |
|--------|----------------------|------------------------------|
| **Prospect financials** | Manually exported from Creditreform / North Data and pasted into JSON | Live Creditreform / D&B API call per prospect |
| **Website signals** | Hand-scraped into markdown | Headless-browser scraper on a weekly cadence + trade press monitoring |
| **LinkedIn stakeholders** | Manually copied into JSON | LinkedIn Sales Navigator API + enrichment vendor (Apollo / Cognism) |
| **Contact details** | Manually pasted from company websites | Direct API integration with enrichment vendor |
| **Customer anchors** | Hand-built JSON files | Live read from Salesforce + ERP |
| **Output delivery** | Markdown files + static HTML app | Push directly into Salesforce as ranked Lead records, with cheat sheet attached |
| **Re-scoring cadence** | On demand (re-run the skill) | Continuous — re-score nightly as inputs change |

The key insight: **Step 4 captures 80%+ of the value without any systems integration.** Every input the agent needs is publicly available; the only thing Step 6 changes is who clicks the export button.

---

## How to Run the Demo

### Prerequisites
- Cursor IDE with agent mode enabled

### Steps

1. **Open this workspace** in Cursor

2. **Review the synthetic data** in [synthetic-data-leads/](synthetic-data-leads/):
   - [prospects/](synthetic-data-leads/prospects/) — 10 manufacturing prospect data packets (automotive, pharma, food, aerospace, electronics, packaging, logistics, medtech)
   - [kuka-customers/](synthetic-data-leads/kuka-customers/) — 4 existing customer anchors
   - [maxm-offering.md](synthetic-data-leads/maxm-offering.md) — the 3-pillar automation offering definition
   - [icp-and-scoring-rubric.md](synthetic-data-leads/icp-and-scoring-rubric.md) — the published 7-factor scoring rubric
   - [data-sources.md](synthetic-data-leads/data-sources.md) — what each file proxies in the real world

3. **Invoke the agent** by asking:
   > "Use the KUKA lead-gen agent skill to score the prospects in `synthetic-data-leads/` and write all outputs to `output-leads/`."

4. **Review the generated outputs** in [output-leads/](output-leads/):
   - [prioritized-prospect-list.md](output-leads/prioritized-prospect-list.md) — ranked prospect list with rationale
   - [prospect-scorecards.json](output-leads/prospect-scorecards.json) — machine-readable scorecards
   - [stakeholder-map.md](output-leads/stakeholder-map.md) — right person to call per prospect
   - [cheat-sheets/](output-leads/cheat-sheets/) — one cold-call cheat sheet per prospect

5. **Open the interactive demo app** by opening [output-leads/app/index.html](output-leads/app/index.html) in a browser:
   - Click **Run lead-gen agent** to play the agent-running animation
   - Sort and filter the prospect pipeline (by priority, size, industry)
   - Click any prospect for the deep-dive view: **Scoring rationale**, **Stakeholder dossier**, **Cold-call cheat sheet** (with copy-to-clipboard buttons per section)

---

## Demo Talking Points

### The agent is reproducible, not a black box

Open [synthetic-data-leads/icp-and-scoring-rubric.md](synthetic-data-leads/icp-and-scoring-rubric.md) — the agent's entire scoring methodology is published. Two runs over the same data produce the same scores. An account manager can read the rubric and predict roughly how the agent will rank a given company. This is essential for building trust.

### Look-alike modelling is the wedge

The most powerful single feature is the look-alike score against KUKA's existing customer book. **Süddeutsche Elektronik is a 92% look-alike to Novara Technologies.** The cheat sheet says: "Dr. Fischer's team at Novara went from 1.8% to 0.4% defects with 10 KUKA robots — same industry, same setup." That sentence is the most credible cold-call opener available, and it can only be written by an agent that has access to both the prospect data *and* the customer book.

### The right stakeholder is rule-based, not a guess

The agent applies a documented rule set:
- HC ≥ 80 + VP Production / Plant Manager present → Production leader is primary
- HC < 80 + no dedicated ops leader → CEO/Owner is primary
- PE-backed with explicit automation mandate → the PE-appointed ops leader overrides

You can read the rule it applied in every scorecard. This means an account manager disagreement with the agent is a productive conversation about the rule, not about a vibe.

### Trigger events make calls timely

For Bavaria Motors the agent flags: "Brand-new VP Production joined Jan 2026 from ZF Friedrichshafen, with Zukunft der Fertigung modernisation programme announced." For Norddeutsche Schweißtechnik: "3 master welders retiring 2026-2027; son Markus visited KUKA open day and published 'Warum wir automatisieren müssen.'" These are the moments where a cold call lands warmest — and the agent surfaces them automatically.

---

## Impact Quantification

### Per-prospect savings

- **Before:** ~2 hours of manual research per qualified lead
- **After:** ~5 minutes (read the cheat sheet, place the call)
- **Net saving:** ~115 minutes per lead

### At scale (illustrative)

- ~50 account managers × 5 qualified leads/week × 45 working weeks = **11,250 leads/year**
- 11,250 × 115 minutes saved = **~21,600 hours/year** returned to client-facing work
- At a blended account manager cost of ~€80/hour = **~€1.7M/year in productivity gains**
- Plus: **higher conversion** because account managers call the right person with the right hook

### Revenue upside from better-targeted prospecting

In the demo run alone, the 7 High-priority prospects represent **~€2,310K in estimated first-year deal value**. Even at 25% conversion that's ~€578K of new revenue from a single 10-prospect batch. Run the agent monthly across the European manufacturing segment and the cumulative pipeline is significant.

### Quality wins

- **Higher first-call meeting rate** — the agent identifies the right decision-maker with the right hook anchored to a real customer parallel
- **Consistent quality across the team** — junior account managers use the same cheat sheets as veterans
- **Compliance & governance** — every score has a published rationale and a traceable source data point; no "gut feel" decisions on who to prioritise

---

## File Structure (lead-gen demo)

```
Kuka/
├── synthetic-data-leads/
│   ├── prospects/                           ← 10 manufacturing prospect data packets
│   │   ├── 01-bavaria-motors/
│   │   ├── 02-rhein-pharma/
│   │   ├── 03-alpen-food/
│   │   ├── 04-precision-aero/
│   │   ├── 05-mueller-kunststoff/
│   │   ├── 06-weber-logistik/
│   │   ├── 07-norddeutsche-schweiss/
│   │   ├── 08-sueddeutsche-elektronik/
│   │   ├── 09-bergmann-verpackung/
│   │   └── 10-fischer-medizintechnik/
│   ├── kuka-customers/                      ← 4 existing-customer anchors
│   │   ├── schneider-automotive.json
│   │   ├── bayer-pharma-pack.json
│   │   ├── hartmann-metallbau.json
│   │   └── novara-technologies.json
│   ├── maxm-offering.md                     ← 3-pillar automation offering
│   ├── icp-and-scoring-rubric.md            ← published 7-factor rubric
│   └── data-sources.md                      ← what each input proxies
├── output-leads/
│   ├── prioritized-prospect-list.md
│   ├── prospect-scorecards.json
│   ├── stakeholder-map.md
│   ├── cheat-sheets/
│   │   └── (10 cheat sheets — one per prospect)
│   └── app/
│       └── index.html                       ← interactive demo UI
└── README-lead-gen.md                       ← this file
```
