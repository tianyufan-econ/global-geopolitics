# Global Geopolitical Events Database

## Overview

The Global Geopolitical Events (GGE) Database compiles 833,485 major bilateral political events between all 193 UN member states from 1950 to 2024. Using large language models to systematically identify, classify, and score events, we construct dynamic measures of bilateral geopolitical alignment that capture the timing and intensity of international relationships.

## Methodology

**Event compilation.** A large language model (Gemini 2.5 Pro) follows a structured 5-step prompt for each country-pair-year: (1) verify historical political entities, (2) search across six event domains, (3) classify each event by category and assign CAMEO and Goldstein codes, (4) assess the overall annual relationship, and (5) output a validated JSON record. Events span six top-level domains -- Economic Relations, Diplomatic and Political, Security and Defense, Legal/Territorial/Movement, Multilateral Governance, and Other -- with 23 subcategories.

**Event coding.** Each event is coded using the CAMEO (Conflict and Mediation Event Observations) framework with 20 root codes across four quad classes (Verbal Cooperation, Material Cooperation, Verbal Conflict, Material Conflict), and assigned a Goldstein Scale score ranging from -10 (maximum conflict) to +10 (maximum cooperation).

**Score construction.** The yearly static score averages all Goldstein scores for a dyad-year, normalized to [-1, +1]. The dynamic score incorporates relationship persistence via an exponential decay model: new events update the score in proportion to their share of the effective cumulative event stock, while years without events see both the score and event stock depreciate at rate 0.3. See the [codebook](./Geopolitical_Scores/codebook.pdf) for full equations and variable definitions.

## Data Access

Bilateral geopolitical alignment scores for 4,332 dyads -- all country pairs involving at least one of the 24 major nations, paired with all 193 UN member states -- are available in the [Geopolitical_Scores](./Geopolitical_Scores) folder, along with a detailed [codebook](./Geopolitical_Scores/codebook.pdf). This is the sample used in Fan (2025), *Measuring Geopolitical Alignment and Economic Growth*.

The full database will be released in phases alongside the companion papers:

- Bilateral alignment scores for all 18,528 UN-member dyads
- Complete event-level records with CAMEO classifications and Goldstein scores

For early access to specific country pairs or samples, please contact Tianyu Fan (tianyu.fan@yale.edu).

## Citation

If you use data from this database, please cite:

> Tianyu Fan (2025). "Measuring Geopolitical Alignment and Economic Growth."

> Tianyu Fan, Mai Wo, and Wei Xiang (2025). "Geopolitical Barriers to Globalization."

## References

**Tianyu Fan (2025).** "Measuring Geopolitical Alignment and Economic Growth." [[arXiv]](https://arxiv.org/abs/2507.04833)

**Tianyu Fan, Mai Wo, and Wei Xiang (2025).** "Geopolitical Barriers to Globalization." [[arXiv]](https://arxiv.org/abs/2509.12084)

**Tianyu Fan, Jizhou Liu, and Wei Xiang (2026).** "The Structure of International Order, 1950--2024." *Coming soon.*
