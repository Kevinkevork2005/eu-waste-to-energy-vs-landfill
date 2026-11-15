# Waste-to-Energy vs. Landfilling in the EU (2005–2023)

**Author:** Kevin Derderian
**Course:** INFO 4617 Web Data Science, Final Project

## Research question

Between 2005 and 2023, did the European Union countries that increased waste-to-energy (WTE) incineration reduce how much they landfill, or did WTE grow at the expense of recycling? Using Eurostat's municipal solid waste data (`env_wasmun`) for the 27 EU member states, collected through Eurostat's public API, this project compares how each country's waste was split between landfilling, energy recovery and recycling over time. The goal is to see whether more WTE goes along with less landfilling while recycling holds steady. The results are aimed at state officials and U.S. waste companies weighing whether to move from landfilling toward WTE.

## Data

### Source
- **Publisher:** Eurostat, the statistical office of the European Union
- **Dataset:** Municipal waste by waste management operations (`env_wasmun`),
  DOI [10.2908/ENV_WASMUN](https://doi.org/10.2908/ENV_WASMUN)
- **Data browser:** https://ec.europa.eu/eurostat/databrowser/view/env_wasmun/default/table
- **API endpoint:** `https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/env_wasmun`
- **Dataset version:** last updated by Eurostat on October 15, 2026
- **License:** Eurostat data may be reused freely with acknowledgment of the source
  ([Eurostat copyright policy](https://ec.europa.eu/eurostat/about-us/policies/copyright)).

### Collection
- **Collected:** November 8, 2026, with `euro_waste_data_collection.ipynb`.
- **robots.txt:** checked first. `https://ec.europa.eu/robots.txt` returned status 200,
  `can_fetch` returned `True` for the API URL, and no crawl delay was set, so I used a
  1-second pause.
- **Method:** Eurostat Statistics API, called with Python `requests`. One request per country
  and waste operation: 27 countries × 7 operations = **189 requests, all 189 successful**.
  Every request sent a User-Agent identifying me and was followed by a 1-second pause.
- **Scope:** EU-27 countries, 2005–2023, kilograms per person (`KG_HAB`).
- **Operations collected:**

| Code | Meaning |
|---|---|
| `GEN` | Waste generated |
| `DSP_L_OTH` | Landfill and other disposal |
| `RCV_E` | Energy recovery (waste-to-energy) |
| `DSP_I` | Incineration without energy recovery |
| `RCY` | Recycling, total |
| `RCY_M` | Recycling, material |
| `RCY_C_D` | Recycling, composting and digestion |

### Files
| File | Contents | Size |
|---|---|---|
| `euro_waste_data_collection.ipynb` | Collection notebook, with outputs | — |
| `eurostat_env_wasmun_raw.json` | The 189 API responses, exactly as returned (1995–2024) | 189 responses, 732 KB |
| `eurostat_waste_2005_2023.csv` | Clean table: `geo`, `wst_oper`, `year`, `kg_per_person` | 3,591 rows (27 × 7 × 19) |

**Missing values:** 38 of 3,591 rows (1.1%) have no value because the country did not report
that year. They are left empty, not filled in. Most are Ireland (14), Croatia (9) and
Denmark (7).

### What I left out and why
- **Years 1995–2004 and 2024:** returned by the API and kept in the raw JSON, but dropped from
  the CSV to match the research question's 2005–2023 window.
- **Eurostat's data-quality flags:** the raw JSON marks some values as estimated (`e`),
  provisional (`p`) or a break in series (`b`). The CSV does not include these flags yet.
  I will add them before the analysis, since breaks in series can create false jumps.
- **Thousand-tonnes unit (`THS_T`):** not collected. Kilograms per person lets countries
  of different sizes be compared directly.
- **Other operations (`TRT`, `DSP_I_RCV_E`, `PRP_REU`):** not collected. `DSP_I_RCV_E` is
  `DSP_I` + `RCV_E` and would double-count; the others are not needed for the question.
- **Non-EU countries and EU-wide totals:** outside the EU-27 scope.
- **Personal data:** none. The data are national totals, with no names or individual records.


*Municipal waste by waste management operations* (`env_wasmun`), retrieved through the [Eurostat dissemination API](https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/env_wasmun). Raw and cleaned files will be stored in [`data/`](data/).

## Methods
The analysis is in analysis.ipynb.

Data quality flags: Eurostat's status flags (estimated, provisional, imputed, break in series) were read from the raw JSON and attached to each value (data/processed/eurostat_waste_2005_2023_flagged.csv).
Shares: Landfill, energy recovery (WTE), incineration without energy recovery and recycling were each divided by waste generated, giving a percentage share for every country and year. The four categories account for a median of 99% of waste generated.
Total incineration: Because some countries (notably Germany and the Netherlands) reclassified existing incinerators from D10 to R1 during the period, WTE alone overstates new capacity. I therefore also use total incineration (D10 + R1).
Change per country: For each country I compared its first and last year with complete data (2005 and 2023 for 24 countries; Croatia starts 2007, the Netherlands 2008, Latvia ends 2022) and calculated the change in percentage points (data/processed/country_changes.csv).
Comparisons: Pearson correlations between the changes (with Spearman rank correlations as a check), and a comparison of countries that started with high incineration (30%+ in 2005) against the rest.
Preliminary findings

Scatter plots of change in incineration vs. change in landfill and recycling

Figure 1. Across the EU-27 from 2005 to 2023, countries that burned more of their waste cut landfilling the most (r = −0.74), but incineration growth had no clear relationship with recycling change (r = 0.09).

Landfill rates fell sharply across the EU. The median country's landfill share fell from 62.8% of waste generated in 2005 to 18.6% in 2023, and from 277.5 to 98 kg per person (about −65%). By 2023, 11 of 26 countries with data were at or below the EU's 2035 target of 10% (AT, BE, DE, DK, EE, FI, LT, LU, NL, SE, SI), while Greece (80.5%), Romania (75.7%), Malta (73.6%), Cyprus (61.4%) and Portugal (56.6%) still landfilled the most.
Incineration replaced landfilling. Countries that expanded incineration cut landfilling the most. Finland, Estonia, Ireland and Lithuania each added 40+ points of incineration and cut landfilling by 46–83 points.
No sign that WTE crowded out recycling overall. Recycling rose in 25 of 27 countries, and its change was unrelated to incineration growth.
Possible plateau among heavy incinerators. Countries already burning 30%+ of their waste in 2005 raised recycling by +6 points versus +22 for the rest, and Sweden's recycling fell. Since these countries also started with three times the recycling, this may reflect a ceiling rather than lock-in.
Limitations: 27 countries is a small sample; correlation does not show cause; 19 countries have at least one break in series; and in a few country-years the shares fall well below or above 100% (59.7%–116.3%).

## Full write-up to come in report.md.

## How to reproduce

To be completed.

## Proposal

The project proposal is in proposal.md.

## Proposal

The project proposal is in [`proposal.md`](proposal.md).

## Data source and license

Data © European Union, Eurostat, reused under the [Eurostat copyright and licence policy](https://ec.europa.eu/eurostat/about-us/policies/copyright).
