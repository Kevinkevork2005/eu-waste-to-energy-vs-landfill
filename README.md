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

_To be completed._ See [`analysis.ipynb`](analysis.ipynb).

## Findings

_To be completed._ See [`report.md`](report.md).

## How to reproduce

_To be completed._

## Proposal

The project proposal is in [`proposal.md`](proposal.md).

## Data source and license

Data © European Union, Eurostat, reused under the [Eurostat copyright and licence policy](https://ec.europa.eu/eurostat/about-us/policies/copyright).
