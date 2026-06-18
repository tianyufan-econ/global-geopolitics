# Global Geopolitical Events Database

**Version 1.0 · June 2026**

The Global Geopolitical Events (GGE) Database compiles 833,485 major bilateral political events between all 193 UN member states from 1950 to 2024. Using large language models to systematically identify, classify, and score events, we construct dynamic measures of bilateral geopolitical alignment that capture the timing and intensity of international relationships.

---

## This Release

Bilateral geopolitical alignment scores for **all 18,528 UN-member dyads** -- every pair among the 193 UN member states -- covering **1950--2024** (1,389,600 dyad-year observations).

The empirical samples of Fan (2025), *Measuring Geopolitical Alignment and Economic Growth* (24 major economies), and Fan, Wo, and Xiang (2025), *Geopolitical Barriers to Globalization* (32 major economies), are subsets of this full panel.

- **Data:** [`Geopolitical_Scores/dyad_geopolitical_scores.zip`](./Geopolitical_Scores/dyad_geopolitical_scores.zip) -- compressed CSV (~28 MB; unzips to a single ~93 MB file)
- **Codebook:** [PDF](./Geopolitical_Scores/codebook.pdf) · [Markdown](./Geopolitical_Scores/codebook.md)
- **Format:** UTF-8 CSV with comma delimiters, readable in R, Python, and Stata. The full panel has 1,389,600 rows, which exceeds Excel's row limit -- use R, Python, or Stata for the complete file.

---

## Citation

If you use data from this database, please cite:

> Tianyu Fan (2025). "Measuring Geopolitical Alignment and Economic Growth." [[arXiv]](https://arxiv.org/abs/2507.04833)
>
> Tianyu Fan, Mai Wo, and Wei Xiang (2025). "Geopolitical Barriers to Globalization." [[arXiv]](https://arxiv.org/abs/2509.12084)

### BibTeX

```bibtex
@unpublished{fan2025geopolitical,
  author = {Fan, Tianyu},
  title  = {Measuring Geopolitical Alignment and Economic Growth},
  year   = {2025},
  note   = {arXiv:2507.04833}
}

@unpublished{fanwoxiang2025barriers,
  author = {Fan, Tianyu and Wo, Mai and Xiang, Wei},
  title  = {Geopolitical Barriers to Globalization},
  year   = {2025},
  note   = {arXiv:2509.12084}
}
```

---

## Forthcoming Releases

Additional data releases are planned alongside future companion papers:

- Complete event-level records with CAMEO classifications and Goldstein scores

For access to additional data, please contact Tianyu Fan (tianyufan.econ@gmail.com).

---

## Methodology

**Event compilation.** A large language model (Gemini 2.5 Pro) follows a structured 5-step prompt for each country-pair-year: (1) verify historical political entities, (2) search across six event domains, (3) classify each event by category and assign CAMEO and Goldstein codes, (4) assess the overall annual relationship, and (5) output a validated JSON record. Events span six top-level domains -- Economic Relations, Diplomatic and Political, Security and Defense, Legal/Territorial/Movement, Multilateral Governance, and Other -- with 23 subcategories.

**Event coding.** Each event is coded using the CAMEO (Conflict and Mediation Event Observations) framework with 20 root codes across four quad classes (Verbal Cooperation, Material Cooperation, Verbal Conflict, Material Conflict), and assigned a Goldstein Scale score ranging from -10 (maximum conflict) to +10 (maximum cooperation).

**Score construction.** The yearly static score averages all Goldstein scores for a dyad-year, normalized to [-1, +1]. The dynamic score incorporates relationship persistence via an exponential decay model: new events update the score in proportion to their share of the effective cumulative event stock, while years without events see both the score and event stock depreciate at rate 0.3. See the [codebook](./Geopolitical_Scores/codebook.pdf) for full equations and variable definitions.

---

## References

**Tianyu Fan (2025).** "Measuring Geopolitical Alignment and Economic Growth." [[arXiv]](https://arxiv.org/abs/2507.04833)

**Tianyu Fan, Mai Wo, and Wei Xiang (2025).** "Geopolitical Barriers to Globalization." [[arXiv]](https://arxiv.org/abs/2509.12084)

**Tianyu Fan, Jizhou Liu, and Wei Xiang (2026).** "The Structure of International Order, 1950--2024." *Coming soon.*
