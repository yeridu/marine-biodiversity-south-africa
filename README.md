# Teleost richness and relative abundance at a reef site and a sandy site in Plettenberg Bay, South Africa (BRUV survey)

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![R](https://img.shields.io/badge/R-%3E%3D4.5-blue.svg)](https://www.r-project.org/)

## Overview

This repository contains the R code and generated outputs for a preliminary, non-extractive survey of teleost (bony fish) richness and relative abundance at two contrasting sites in Plettenberg Bay, South Africa -- a rocky-reef site and a sandy site -- recorded with Baited Remote Underwater Video (BRUV) during 2024.

The analysis is **descriptive and site-specific**. Because the 2024 design sampled a single fixed reef site and a single fixed sandy site, habitat type, depth, and marine-protected-area status are confounded and habitat is not replicated; the code therefore describes differences between the two sampled sites and does **not** test general reef-versus-sand, MPA, or seasonal effects.

## Authors

Hannah Mary Lazenby, Mario Morales, Melissa Nel, Chantel Elston, Alejandra Vargas-Fonseca.

Repository maintained by Mario Morales -- [ORCID: 0000-0002-0576-8655](https://orcid.org/0000-0002-0576-8655).

## Summary of findings

Eighteen usable BRUV deployments were obtained across 15 sampling dates (14 at the reef site, 4 at the sandy site); two recorded no teleost. Across both sites, 25 teleost species from 10 families were recorded, dominated by the Sparidae. Per-deployment species richness was higher at the reef site (median 6, range 0-14) than at the sandy site (median 2, range 0-5); the most frequent and abundant taxa were Strepie (*Sarpa salpa*), Blacktail (*Diplodus capensis*), and Steentjie (*Spondyliosoma emarginatum*). Conservation-relevant species recorded include the Endangered White steenbras (*Lithognathus lithognathus*), the Vulnerable White stumpnose (*Rhabdosargus globiceps*), and the Data Deficient Santer (*Cheimerius nufar*).

## The observational unit

The raw BRUV file stores long recordings as multiple GoPro video **chapters** (for example, files `GH010084`-`GH070084` are seven chapters of one recording). The correct sampling unit is the complete **deployment**, not the chapter. The script groups chapters into deployments by date, site, and recording identifier, and computes each species' deployment-level MaxN as the **maximum across that deployment's chapters** (never the sum). Valid no-fish recordings are retained as biological zeros.

## Repository structure

```
bruv-analysis/
├── data/
│   ├── raw/                         # BRUV data (available on request; see Data availability)
│   └── processed/                   # Derived deployment-level data (available on request)
├── scripts/
│   └── morales_teleost6.Rmd         # Analysis script (modular, fully reproducible)
├── figures/                         # Generated figures (PNG, 300 DPI)
│   ├── v6_Fig1_design.png           # Sampling design / effort by date and site
│   ├── v6_Fig2_richness.png         # Per-deployment richness by site
│   ├── v6_Fig3_occurrence.png       # Frequency of occurrence by species and site
│   └── v6_FigS1_heatmap.png         # Deployment x species MaxN (exploratory)
├── output/                          # Generated tables and verification log
│   ├── v6_key_results.csv           # Single numeric source of truth
│   ├── v6_species_site.csv          # Species table (occurrence and MaxN by site)
│   ├── v6_species_table.docx        # Formatted species table
│   └── v6_VERIFICATION_LOG.txt      # Automated check results
├── .gitignore
└── README.md
```

## Data availability

The BRUV data are **available on request** and will be made publicly available upon publication. For early access for research purposes, contact Mario Morales (mariomorales@arizona.edu).

When available, the script reads the chapter-level recording file from `data/raw/` and writes the deployment-level matrix and chapter-to-deployment crosswalk to `data/processed/`.

## Analyses

The analysis is deliberately descriptive, which is the defensible approach for this sample size (n = 14 reef, 4 sandy) and confounded, unreplicated design.

| Step | Method | R package |
|------|--------|-----------|
| Chapter-to-deployment crosswalk | Group video chapters into deployments; verify against the field deployment log | base R |
| Relative abundance | Deployment-level MaxN = maximum across chapters | base R |
| Within-site diversity | Species richness and Shannon index (H', natural log) per deployment | `vegan` |
| Occurrence and abundance | Frequency of occurrence (n/N) and mean MaxN per site | `vegan`, `flextable` |
| Between-site contrast | Descriptive only (medians, ranges; Cliff's delta as a descriptive effect size) | `effectsize` |

The pipeline is written as reusable functions (`read_fishdata` -> `build_crosswalk` -> `aggregate_maxn` -> summaries / figures) so additional, habitat-replicated data can be incorporated later without rewriting.

## Reproducibility

### Software

- **R** >= 4.5 (developed under R 4.5.2)

### Required R packages

```r
install.packages(c(
  "tidyverse",   # data wrangling and visualization
  "readxl",      # read the recording file
  "vegan",       # richness and diversity
  "effectsize",  # descriptive effect sizes
  "here",        # portable file paths
  "officer",     # Word output
  "flextable"    # formatted tables
))
```

### How to reproduce

1. Clone the repository:
   ```bash
   git clone https://github.com/yeridu/marine-biodiversity-south-africa.git
   ```
2. Obtain the data (see Data availability) and place the recording file in `data/raw/`.
3. Open `bruv-analysis.Rproj` in RStudio.
4. Knit `scripts/morales_teleost6.Rmd`, or run:
   ```r
   rmarkdown::render("scripts/morales_teleost6.Rmd")
   ```
5. Figures are written to `figures/` and tables to `output/`.

### Verification

The script embeds automated checks (`stopifnot`) that are summarised in `output/v6_VERIFICATION_LOG.txt`: 98 chapters resolve to exactly 18 deployments (14 reef, 4 sandy); every chapter maps to one deployment and the structure matches the field log; deployment MaxN equals the chapter maximum; biological-zero deployments are retained; and the species matrix matches the explicit 25-species list. Conservation status follows the IUCN Red List of Threatened Species (www.iucnredlist.org). `set.seed(42)` is used for reproducibility.

### Portability

The project uses the [`here`](https://here.r-lib.org/) package for file paths and runs on Windows, macOS, and Linux without modification.

## License

This work is licensed under a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

## Citation

If you use this code, please cite:

```
Lazenby, H.M., Morales, M., Nel, M., Elston, C., Vargas-Fonseca, A. (2025).
Teleost species richness and relative abundance at a reef site and a sandy site
in Plettenberg Bay, South Africa: a preliminary Baited Remote Underwater Video
(BRUV) survey. Code repository. GitHub.
https://github.com/yeridu/marine-biodiversity-south-africa
```

## Contact

Mario Morales -- mariomorales@arizona.edu -- GitHub [@yeridu](https://github.com/yeridu)
