---
title: "Dyad Geopolitical Scores -- Variable Codebook"
author: ""
date: ""
---

# Dataset Overview

**File:** `dyad_geopolitical_scores_Major24xAll.csv`

**Unit of observation:** Dyad-year (undirected, alphabetically ordered by ISO 3166-1 alpha-3 code)

**Time coverage:** 1950--2024 (75 years)

**Country coverage:** 4,332 dyads in which at least one country belongs to the 24 major nations. Each of the 24 major nations is paired with all 193 UN member states.

**Total observations:** 324,900 (4,332 dyads $\times$ 75 years)

**Panel type:** Balanced (all dyad-year cells present; zero-event cells recorded as 0)

**Source data:** Global Geopolitical Events (GGE) Database, v1

**Scope note:** This file covers the sample used in *Fan (2025), "Measuring Geopolitical Alignment and Economic Growth"* -- all bilateral pairs involving at least one of the 24 major nations. The full-coverage panel (all 18,528 UN-member dyads) will be released alongside the companion measurement paper, *Fan, Liu, and Xiang (2026), "The Structure of International Order, 1950--2024."*

## Major 24 Nations

| Code | Country | Code | Country | Code | Country |
|------|---------|------|---------|------|---------|
| ARG | Argentina | ESP | Spain | KOR | South Korea |
| AUS | Australia | FRA | France | MEX | Mexico |
| BEL | Belgium | GBR | United Kingdom | NLD | Netherlands |
| BRA | Brazil | IDN | Indonesia | POL | Poland |
| CAN | Canada | IND | India | RUS | Russia |
| CHE | Switzerland | ITA | Italy | SAU | Saudi Arabia |
| CHN | China | JPN | Japan | TUR | Turkey |
| DEU | Germany | DNK | Denmark | USA | United States |

---

# Variable Definitions

## Identifiers

| Variable | Type | Description |
|----------|------|-------------|
| `dyad` | String | Country pair identifier in alphabetical order (e.g., `CHN-USA`, `DEU-FRA`) |
| `country1_code` | String | ISO3 code of the first country (alphabetically earlier) |
| `country2_code` | String | ISO3 code of the second country |
| `year` | Integer | Calendar year (1950--2024) |

## Primary Geopolitical Measures

| Variable | Type | Range | Description |
|----------|------|-------|-------------|
| `geo_score` | Float | $[-1, 1]$ | **Static geopolitical score.** Annual average of Goldstein Scale values divided by 10. Missing if no events observed. |
| `geo_score_dyn` | Float | $[-1, 1]$ | **Dynamic geopolitical score.** Exponentially weighted score incorporating historical information with depreciation rate $\delta = 0.3$. Never missing; decays toward 0 during periods without events. |
| `geo_score_ma` | Float | $[-1, 1]$ | **Moving average geopolitical score.** Event-weighted average of `geo_score` over a 3-year window ($t-2$ to $t$). Missing if no valid observations in window. |

## Auxiliary Variables

| Variable | Type | Range | Description |
|----------|------|-------|-------------|
| `number_of_events` | Integer | $[0, \infty)$ | Count of bilateral geopolitical events in the given year |
| `cum_events` | Float | $[0, \infty)$ | Effective cumulative event count with exponential depreciation |
| `weight` | Float | $[0, 1]$ | Updating weight $\phi_{ij,t}$ assigned to the current year's score in the dynamic calculation |

---

# Score Construction

## Static Score

Let $\{s^{n}_{ij,t}\}_{n=1}^{\tilde{N}_{ij,t}}$ denote the Goldstein scores for the set of events between countries $i$ and $j$ in year $t$, where $\tilde{N}_{ij,t}$ is the number of events. The yearly average event score, normalized to the $[-1, +1]$ interval, is:
$$\tilde{S}_{ij,t}=\frac{1}{\tilde{N}_{ij,t}} \sum_{n=1}^{\tilde{N}_{ij,t}} s_{ij,t}^n \,/\, 10$$

This corresponds to the variable `geo_score`. It is missing (NaN) for dyad-years with no observed events.

## Dynamic Score

The dynamic score balances the contemporaneous impact of political events with the institutional memory that characterizes interstate relations. When events are observed ($\tilde{N}_{ij,t} > 0$):
$$S_{ij,t}=\left(1-\phi_{ij,t}\right) \cdot S_{ij,t-1}+\phi_{ij,t} \cdot \tilde{S}_{ij,t}$$
where the updating weight and effective cumulative event count are:
$$\phi_{ij,t} = \frac{\tilde{N}_{ij,t}}{N_{ij,t}}, \qquad N_{ij,t} = \left(1 - \delta\right) N_{ij,t-1} + \tilde{N}_{ij,t}$$

When no events are observed ($\tilde{N}_{ij,t} = 0$), the update equation does not apply; instead, both the score and effective event count decay:
$$S_{ij,t} = (1-\delta)\, S_{ij,t-1}, \qquad N_{ij,t} = (1-\delta)\, N_{ij,t-1}$$

**Initialization.** For the first year of each dyad ($t = t_0$): $S_{ij,t_0} = \tilde{S}_{ij,t_0}$ and $N_{ij,t_0} = \tilde{N}_{ij,t_0}$. When the initial year has no events, $S_{ij,t_0} = 0$ and $N_{ij,t_0} = 0$.

The depreciation rate is set to $\delta = 0.3$. The updating weight $\phi_{ij,t}$ determines how responsive the score is to new information: country pairs with long interaction histories have large $N_{ij,t}$, so any single year's events receive a small weight, reflecting the difficulty of fundamentally shifting a deeply established relationship. Conversely, pairs with sparse histories update more rapidly.

This corresponds to the variable `geo_score_dyn`. It is never missing; it equals 0 for dyads with no prior events and decays toward 0 during periods without events.

## Moving Average Score

The moving average score is an event-weighted average of `geo_score` over a 3-year window ($t-2$ to $t$):
$$S^{\text{ma}}_{ij,t} = \frac{\sum_{k=t-2}^{t} \tilde{N}_{ij,k} \cdot \tilde{S}_{ij,k}}{\sum_{k=t-2}^{t} \tilde{N}_{ij,k}}$$
where the sum includes only years with non-missing scores. Missing if no events occur in the entire window.

## Key Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| $\delta$ | 0.3 | Depreciation rate for dynamic scores and cumulative events |
| MA window | 3 years | Moving average window ($t-2$ to $t$) |

---

# Goldstein Scale Interpretation

The underlying Goldstein Scale ranges from $-10$ (maximum conflict) to $+10$ (maximum cooperation). All `geo_score` variants are normalized by dividing by 10:

| Score range | Interpretation |
|-------------|---------------|
| $-1.0$ to $-0.6$ | Severe conflict (military attacks, war, use of force) |
| $-0.6$ to $-0.3$ | Significant hostility (military threats, sanctions, breaks in relations) |
| $-0.3$ to $\phantom{-}0.0$ | Minor conflict (diplomatic protests, recalls, verbal hostility) |
| $\phantom{-}0.0$ to $+0.3$ | Neutral to mild cooperation (routine diplomatic exchanges, visits) |
| $+0.3$ to $+0.6$ | Moderate cooperation (agreements, treaties, joint statements) |
| $+0.6$ to $+1.0$ | Strong cooperation (military alliance, economic integration, extensive aid) |

---

# Event Data Methodology

## Overview

The underlying event data were compiled by a large language model (Gemini 2.5 Pro) following a structured prompt. Each event represents a distinct significant bilateral political interaction between two countries in a given year.

**Total events in the GGE database:** 833,485 across all 18,528 dyads among 193 UN member states, 1950--2024.

## Event Identification Pipeline

The LLM follows a 5-step analytical procedure for each dyad-year:

1. **Entity verification.** Confirm that both countries existed as the specified political entities in the target year; if not, identify the appropriate historical predecessor (e.g., Soviet Union before December 1991).
2. **Systematic search.** Search across all six event domains (below) using official government sources, international organizations, major news archives, and academic sources.
3. **Event coding.** Classify each event by category (A1--F7), assign CAMEO quad class and root code, and calibrate a Goldstein score ($-10$ to $+10$).
4. **Relationship assessment.** Assign an overall annual assessment using a 9-point ordinal scale ranging from *State of War / Active Conflict* to *Alliance*.
5. **Structured output.** Generate a validated JSON object with 17 required fields per event.

## Event Taxonomy

Events are classified into 6 top-level domains with 23 subcategories:

**A. Economic Relations** (6 subcategories)

- A1. Trade Policy and Market Access -- tariffs, non-tariff barriers, trade agreements
- A2. Financial and Monetary Relations -- financial sanctions, monetary cooperation, investment controls
- A3. Economic Coercion and Incentives -- comprehensive sanctions, export controls, economic aid
- A4. Strategic Economic Sectors -- energy, technology/digital, critical resources
- A5. Economic Integration and Infrastructure -- BRI/competing programs, supply chains, regional arrangements
- A6. Other Economic Events -- market access issues, boycotts, gray zone actions

**B. Diplomatic and Political Relations** (4 subcategories)

- B1. Formal Diplomatic Engagement -- embassy actions, ambassador recalls, diplomatic protests
- B2. High-Level Political Interactions -- summits, ministerial meetings, strategic dialogues
- B3. Public Diplomacy and Rhetoric -- policy speeches, parliamentary resolutions, propaganda
- B4. Cultural and Educational Diplomacy -- cultural agreements, scholarship programs, academic exchanges

**C. Security and Defense** (3 subcategories)

- C1. Military Cooperation and Competition -- defense agreements, arms sales, joint exercises
- C2. Security Incidents and Responses -- border skirmishes, airspace violations, territorial claims
- C3. Intelligence and Cyber -- espionage, cyber attacks, intelligence sharing

**D. Legal, Territorial, and Movement** (3 subcategories)

- D1. International Legal Actions -- ICJ/WTO cases, extradition, legal cooperation
- D2. Territorial and Maritime Issues -- EEZ disputes, border demarcation, sovereignty claims
- D3. Movement of People -- visa policies, migration agreements, travel bans

**E. Multilateral and Global Governance** (2 subcategories)

- E1. International Organizations -- UN system, regional organization actions
- E2. Global Issues Cooperation/Competition -- climate, health, human rights

**F. Other Significant Events** (5 subcategories)

- F1. Historical and Symbolic Events -- apologies, memorial visits, symbolic gestures
- F2. Natural Disasters and Humanitarian Crises -- disaster response, crisis management
- F3. Sports and Major Events -- Olympic boycotts, major event hosting
- F4. Technology and Space -- joint missions, technology disputes
- F5--F7. Environmental, Communications, and Other -- transboundary issues, media relations, other interactions

## CAMEO Framework

Each event is classified using the Conflict and Mediation Event Observations (CAMEO) framework into one of four quad classes:

| Quad class | CAMEO root codes | Examples |
|------------|-----------------|----------|
| Verbal Cooperation | 01--05 | Public statements, appeals, diplomatic cooperation |
| Material Cooperation | 06--08 | Material aid, economic cooperation, concessions |
| Verbal Conflict | 09--14 | Demands, disapproval, threats, protests |
| Material Conflict | 15--20 | Force posture, reduced relations, coercion, assault |

The 20 CAMEO root codes provide a standardized two-digit classification of event types, enabling comparison with other political event datasets.

## Goldstein Scale

The Goldstein scale assigns each event a cooperation--conflict intensity score:

- **$-10$ to $-8$:** Highly conflictual (military attacks, mass violence)
- **$-8$ to $-5$:** Threats, coercion, severe sanctions
- **$-5$ to $-2$:** Diplomatic protests, rejections, minor sanctions
- **$-2$ to $0$:** Mild disagreements, negative rhetoric
- **$0$ to $+2$:** Initial positive gestures, routine exchanges
- **$+2$ to $+5$:** Consultations, cooperative intent, diplomatic meetings
- **$+5$ to $+8$:** Significant aid, major agreements, material cooperation
- **$+8$ to $+10$:** Major concessions, peace agreements, alliance formations

Scores are calibrated against CAMEO root codes and adjusted for bilateral context.

---

# Usage Notes

1. **Balanced panel.** The dataset includes all 4,332 dyad-year combinations within the time range, including years with no events (`number_of_events = 0`).

2. **Dyad directionality.** Dyads are undirected. The two ISO3 codes are always arranged alphabetically (e.g., `CHN-USA`, never `USA-CHN`).

3. **Historical entities.** Country codes reflect present-day ISO3 assignments. Events involving predecessor states (e.g., the Soviet Union before 1991) are coded under the successor state's code (e.g., `RUS`).

4. **Recommended usage:**
   - Use `geo_score_dyn` for cross-sectional analysis, as it reflects the cumulative geopolitical alignment status of each dyad at a given point in time.
   - Use `geo_score_dyn` for time-series analysis, with `geo_score` as a robustness check to verify that results are not driven by the smoothing procedure.
   - Use `geo_score_ma` as an additional robustness check or to reduce measurement error from year-to-year volatility.

5. **Interpretation of sign:**
   - Negative values indicate conflict, hostility, or deteriorating relations.
   - Positive values indicate cooperation, partnership, or improving relations.
   - Values near zero indicate neutral or minimal interaction.

---

# References

Tianyu Fan (2025). "Measuring Geopolitical Alignment and Economic Growth." *Working Paper, Yale University.* [arXiv:2507.04833](https://arxiv.org/abs/2507.04833)

Tianyu Fan, Mai Wo, and Wei Xiang (2025). "Geopolitical Barriers to Globalization." *Working Paper, Yale University.* [arXiv:2509.12084](https://arxiv.org/abs/2509.12084)

Tianyu Fan, Jizhou Liu, and Wei Xiang (2026). "The Structure of International Order, 1950--2024." *Working Paper, Yale University.* *Coming soon.*
