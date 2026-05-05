# Ideal Customer Profile (ICP) and Scoring Rubric

This document is the **single source of truth** that the lead-gen agent uses to score prospects. It is published so the agent's outputs are reproducible and defensible — any account manager should be able to read it and predict roughly how the agent will rank a given company.

---

## Ideal Customer Profile

The KUKA industrial automation sweet spot is a European manufacturer that:

1. Has between **20 and 500 employees** (below 20 the capex rarely justifies a direct sale; above 500 there is typically an incumbent robot vendor and long procurement cycles)
2. Generates **€5M - €200M in annual revenue** with positive EBIT or controlled losses (ability to invest)
3. Operates in a sector where **robotic automation is high-impact** (welding, assembly, palletising, machine loading, painting, packaging, quality inspection)
4. Is **growing or stable** (3-yr revenue/headcount trend flat-to-up)
5. Has an **identifiable production/operations decision-maker** reachable through public channels
6. Is **not already** a KUKA customer (assumed true for everything in `prospects/`)

---

## Scoring Rubric (7 Factors, Weighted to 100)

Each factor is scored **0 to 5** with a one-line justification grounded in the source data, then multiplied by its weight. The weighted scores are summed to a `total_score` of 0-100.

| # | Factor | Weight | Description |
|---|--------|--------|-------------|
| 1 | Financial fit | 18 | Revenue band + 3-yr trend + EBIT health |
| 2 | Headcount fit | 18 | Where the company sits in the manufacturing SME band, and headcount trajectory |
| 3 | Industry automation-intensity | 14 | How much robotic automation matters in this industry |
| 4 | Look-alike to existing book | 18 | Closeness to one of KUKA's existing customer anchors |
| 5 | Stakeholder identifiability | 12 | Can we point to a clear named decision-maker? |
| 6 | Contact-channel completeness | 8 | Phone + email + address present? |
| 7 | Trigger events | 12 | Recent change that creates an opening |
| | **Total** | **100** | |

`total_score` is `Σ (factor_score × weight) / 5`, normalised to 0-100.

### Priority bands

- **High priority:** ≥ 70
- **Medium priority:** 50 - 69
- **Low priority:** < 50

---

## Factor 1 — Financial Fit (weight 18)

| Score | Profile |
|-------|---------|
| 5 | Revenue €10M-€150M, 3-yr revenue CAGR ≥ 10%, EBIT margin ≥ 6% |
| 4 | Revenue €5M-€200M, growing, EBIT positive |
| 3 | Revenue in band, flat trend, EBIT marginally positive |
| 2 | Revenue in band but declining, or EBIT negative for 1 year |
| 1 | Revenue < €5M or > €200M, or EBIT negative for 2+ years |
| 0 | Recently founded with no revenue history, or in distress |

---

## Factor 2 — Headcount Fit (weight 18)

| Score | Profile |
|-------|---------|
| 5 | 80-250 FTE, growing |
| 4 | 50-79 or 251-500 FTE, growing or stable |
| 3 | 30-49 FTE, growing |
| 2 | 20-29 FTE, or 500-700 FTE |
| 1 | 10-19 FTE |
| 0 | < 10 FTE or > 700 FTE |

---

## Factor 3 — Industry Automation-Intensity (weight 14)

| Score | Industry types |
|-------|---------------|
| 5 | Automotive (OEM & Tier 1-2 suppliers), pharma/medical device manufacturing, electronics assembly — high-precision, high-volume, regulated |
| 4 | Metal fabrication, plastics & injection moulding, food & beverage packaging, aerospace components — process-intensive, robot-ready |
| 3 | General manufacturing, consumer goods, warehouse/logistics — moderate automation potential |
| 2 | Low-volume craft manufacturing, textiles, agriculture — limited robot applicability |
| 1 | Service industries, pure software, retail — not a KUKA target |
| 0 | Industry where robotic automation is irrelevant |

---

## Factor 4 — Look-Alike to Existing KUKA Book (weight 18)

For each prospect, compute a similarity score against each of the 4 customer anchors in `kuka-customers/` using these attributes:

- Industry match (exact = +2, adjacent = +1)
- Size band match (same band ±1 = +1, exact = +2)
- Ownership type match (founder-led vs PE-backed vs family vs corporate) = +1 if match
- Growth profile match (growing / stable / declining) = +1 if match
- Geography match (same region/country) = +0.5

Cap raw similarity at 6, then map to a 0-5 score: divide by 6 and multiply by 5, round.

Record the **closest anchor**, the **similarity %**, and the **shared attributes** in the output.

---

## Factor 5 — Stakeholder Identifiability (weight 12)

| Score | Situation |
|-------|-----------|
| 5 | A named Head of Production / VP Operations / Plant Manager OR CTO with full title visible in `linkedin-stakeholders.json` |
| 4 | A named CEO / Managing Director with email/phone available |
| 3 | A named senior person but title not perfectly aligned (e.g. "Quality Director" at a small company) |
| 2 | Only mid-level employees visible; no clear decision-maker |
| 1 | Only company name; no individuals |
| 0 | No stakeholder data at all |

---

## Factor 6 — Contact-Channel Completeness (weight 8)

| Score | Situation |
|-------|-----------|
| 5 | Phone + email + postal address all present |
| 3 | Two of the three present |
| 1 | One of the three present |
| 0 | None present |

---

## Factor 7 — Trigger Events (weight 12)

A trigger event is something in `website-snapshot.md` or in the headcount trend that signals openness to a conversation. Score the **strongest single trigger** present:

| Score | Trigger |
|-------|---------|
| 5 | New production facility, major capex announcement, or new senior hire (esp. VP Ops/Production) within last 12 months |
| 4 | Headcount surge ≥ 25% in last 12 months OR major new customer/contract win |
| 3 | Stable hiring, recent product launch or capacity expansion |
| 2 | Recent leadership statement about growth/Industry 4.0/automation |
| 1 | No clear trigger but no negative signals either |
| 0 | Negative signals (layoffs, plant closures, distress) |

---

## Estimated Annual Deal Value (K€)

Compute a rough sizing using the heuristic from `kuka-offering.md`:

```
estimated_deal_value_keur =
    pillar1_robots (200 if greenfield, 300 if expansion, 0 if service-only)
  + pillar2_digital  (5 × estimated_robot_fleet_size)
  + pillar3_service  (12 × estimated_robot_fleet_size)
```

Round to the nearest €5K. This is a **rough indicator**, not a forecast — the cheat sheet does not promise this number to the prospect.

---

## What the Agent MUST NOT Do

- Do **not** invent financial figures, employee names, or trigger events that aren't in the source data
- Do **not** assign a score without a one-line justification quoting/paraphrasing the source data
- Do **not** mark a prospect as High priority on look-alike alone if Headcount Fit < 3 or Financial Fit < 3 (sanity guard)
