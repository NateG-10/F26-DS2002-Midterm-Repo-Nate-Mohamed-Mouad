# Team M&Ms: Hurricane Helene Walmart Demand Analysis

DS 2002 (Fall 2026) midterm project. We clean a month of Walmart Florida transaction data from September 2024, join it to historical weather, and ask whether and how demand changed around Hurricane Helene (landfall September 26, 2024). The full analysis is in one notebook: `notebooks/team_notebook.ipynb`.

## Team

| Member | GitHub | Main work |
|---|---|---|
| Nate | NateG-10 | Q1 demand surge, Q4 stores and geography, notebook integration |
| Mohamed | Moehassan09 | Weather API and join, Q3 weather correlation, charts and presentation |
| Mouad | Mouad-Tazka | Data cleaning, decision log, Q2 SKU consolidation, Q5 board games |

Team roles and working agreements are in [CHARTER.md](CHARTER.md).

## Headline findings

- **Q1, demand surge:** Positive reported SKU units averaged 1,089 per day in the September 1-4 baseline. They rose to 3,190 per day on September 10-14 (+192.9%), peaking on September 14, 12 days before landfall. The required pre-landfall week (September 19-25) averaged 1,107.4 per day, only +1.69% over baseline, and that small increase reverses (0.865x) if the baseline is September 1-7.
- **Q2, SKU consolidation:** The 45 catalog SKU codes group into 20 proposed product families. The four strawberry Pop-Tarts SKUs total 5,198 positive reported units, versus 1,381 if only `PT-12` is counted. The families still need package-size and supplier verification before they could be used for restocking.
- **Q3, weather correlation (Orlando store FL-239):** Units peaked at 291 on September 13 (3.25x baseline), 13 days before landfall. Wind peaked on landfall day, when sales were already normal, and wind is weakly negatively correlated with units (same day r = -0.24). The closest link is rainfall six days earlier (r = 0.53, n = 24), which is an association from one store and one month, not proof of cause.
- **Q4, stores and geography:** All 12 stores show the early spike (2.63x to 3.18x their own baseline), while September 19-25 ratios stay between 0.917x and 1.103x. Distance to the advisory 9 forecast path shows no meaningful relationship with pre-landfall growth (Pearson r = +0.090, 11 stores with coordinates).
- **Q5, board games, signal or artifact:** Entertainment demand rose during September 10-14 in both the transaction CSV (+191.7%) and the SQLite summary (+185.4%). Monopoly alone rose about +257.1% in the CSV, and below-reorder entertainment inventory snapshots went from 0 of 63 to 41 of 71. The two sources disagree on absolute units, so we recommend a modest, monitored inventory increase rather than a large order.

## How to run

1. Clone the repo and keep the folder layout as it is. The notebook finds the repo root and reads from `data/`.
2. Install these packages: `pandas`, `numpy`, `matplotlib`, `requests`, and `ipython`/`jupyter` to run the notebook. `sqlite3`, `pathlib`, `re`, and `time` are in the standard library.
3. Open `notebooks/team_notebook.ipynb` and choose Restart and Run All.
4. Internet access is needed for the weather step in Q3, which calls the Open-Meteo historical archive API. The call has a timeout and retries on rate-limit and server errors.

Running the notebook writes charts and tables to `outputs/` and decision-log files to `data/`.

## Repo layout

```
.
├── CHARTER.md                         team goals, roles, and ways of working
├── README.md
├── data/
│   ├── walmart_transactions.csv       raw transactions, the main dataset
│   ├── store_locations.csv            store IDs, cities, coordinates
│   ├── product_catalog.csv            SKUs, names, categories, package sizes
│   ├── distribution_centers.csv       distribution center capacity and cost
│   ├── inventory_and_sales.db         SQLite: daily_sales_summary, inventory_levels
│   ├── nhc_helene_advisory_009.txt    saved NHC forecast advisory 9 text (Q4)
│   ├── nhc_helene_advisory_009.md     source and verification notes for the advisory
│   ├── decision_log_*.csv             cleaning decision logs written by the notebook
│   └── sku_mapping_*.csv              SKU-to-product-family mappings (draft, audit, full review)
├── notebooks/
│   ├── team_notebook.ipynb            the deliverable: cleaning, weather join, Q1-Q5
│   ├── Kickoff.ipynb                  assignment kickoff
│   └── scratch/                       each member's working notebooks and course studio/lab notebooks
└── outputs/                           charts (.png) and supporting tables (.csv) saved by the notebook
```

## Data and sources

- **Transactions, stores, products, distribution centers, and the SQLite database** are the course-provided project files in `data/`.
- **Weather:** [Open-Meteo Historical Weather API](https://archive-api.open-meteo.com/v1/archive), requesting daily precipitation sum and maximum wind speed at the Orlando store's coordinates with `timezone=America/New_York`.
- **Storm information:** the NHC landfall update (https://www.nhc.noaa.gov/archive/2024/al09/al092024.update.09270318.shtml) and NHC forecast advisory 9, issued September 25, 2024 at 15:00 UTC (https://www.nhc.noaa.gov/archive/2024/al09/al092024.fstadv.009.shtml). The advisory text was retrieved from a NOAA mirror and is saved in `data/` with its source notes.
- Sales timestamps have no timezone. We assume they are America/New_York local time.

## Cleaning approach and key decisions

The goal was to clean without discarding more information than necessary. The notebook's Cleaning Decision Log section gives each decision with its row counts and reason. In short:

- **Removed:** 30 exact duplicate transaction rows.
- **Standardized:** store IDs (case, spacing, separators), SKU formatting, category labels, and text prices converted to numbers. Original columns are kept for comparison.
- **Kept and flagged:** rows with negative quantities (possible returns) and rows with missing timestamps (884 rows), because we should not guess when a purchase happened. Undated rows are excluded only from daily comparisons.
- **Dates:** mixed timestamp formats are parsed and reduced to daily date keys so sales can be joined to daily weather. The weather join is checked for row-count and total preservation.
- **SKUs:** original SKU codes are preserved and mapped to proposed product families without changing transaction row counts or quantities.

## Notebook map

| Section | Content |
|---|---|
| 1. Data Cleaning Pipeline | Cleaning functions and the run that produces the clean transaction and store tables |
| 2. Cleaning Decision Log | Table of each problem, the decision, and the reason |
| 3. SKU Consolidation (Q2) | Pop-Tarts mapping first, then the full 20-family review with before and after chart |
| Q1 | Demand surge: baseline vs. September 10-14 vs. September 19-25, category rankings, charts |
| Q3 | Weather API function, checked join, lag correlations, chart, slide content and speaker notes |
| Q4 | Store-level comparison, distance to the advisory 9 forecast path, charts |
| Q5 | SQLite and CSV cross-check for board games, inventory pressure, recommendation |

Charts saved to `outputs/`: `Q3_orlando_units_vs_weather.png`, `Q5_SQL_vs_CSV_spike.png`, and `Q5_inventory_crosscheck.png`, plus Q5 comparison and inventory audit tables (`Q5_entertainment_source_comparison.csv`, `Q5_inventory_window_audit.csv`). The other charts display in the notebook.
