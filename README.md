# Large-Scale Geospatial Data Processing for Rooftop PV Screening Using GeoParquet: Spatial Attribution and National Building-Footprint Analysis

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![GeoParquet](https://img.shields.io/badge/Format-GeoParquet_1.1-green.svg)](https://geoparquet.org/)

A reproducible, modular geospatial big data processing architecture for national-scale rooftop photovoltaic (PV) screening using columnar **GeoParquet** storage, projection-aware metric area extraction, and explicit regional spatial attribution.

---

## Overview

Assessing distributed rooftop PV potential at a national scale is constrained by storage overhead, coordinate-system requirements, and the complexities of spatial attribution in multi-million-polygon building-footprint inventories. This repository provides the computational workflow accompanying the paper *"Large-Scale Geospatial Data Processing for Rooftop PV Screening Using GeoParquet: Spatial Attribution and National Building-Footprint Analysis"*.

Evaluated across **15,189,620 unique building footprints** in Bangladesh, the framework demonstrates:

* **77.7% Persistent Storage Reduction:** Converts tiled, line-delimited GeoJSON files (`6.85 GB` across 49 files) into spatially organized columnar GeoParquet (`1.53 GB` across 53 files) in `EPSG:4326`.
* **Projection-Aware Spatial Extraction:** Automatically selects local Universal Transverse Mercator (UTM) projections (`EPSG:32645` / `EPSG:32646`) from Area of Interest (AOI) centroids, applies bounding-box candidate prefiltering, and executes exact polygon-intersection tests.
* **Explicit Spatial Attribution Accounting:** Quantifies boundary-crossing duplication under non-clipping inclusion rules, tracking `18,777,917` footprint-region occurrences from `15,189,620` unique footprints (**23.6% occurrence-level attribution excess**).
* **Maximum Geometric Envelope ($\mu = 1$):** Establishes a transparent upper-bound geometric baseline (`160.89 GWp` nominal capacity; `228.43 TWh/yr` reference-yield generation) alongside utilization sensitivity scenarios ($\mu \in \{0.60, 0.30\}$).

> **Methodological Disclaimer:** All capacity and energy outputs represent deterministic, geometric scenario baselines derived from 2D horizontal building-footprint polygons. The workflow does not model roof tilt, orientation, shading, superstructures, structural suitability, or grid constraints, and outputs should not be interpreted as validated technical deployment potential.

---

## Workflow Architecture

The pipeline decouples persistent data management, spatial indexing, metric geometry computation, PV scenario modeling, and regional attribution across eight sequential stages:

```text
[1. Data Ingestion & Validation] 
        │
        ▼
[2. GeoJSON → GeoParquet Conversion (EPSG:4326)] 
        │
        ▼
[3. Spatial Tiling & Indexing] 
        │
        ▼
[4. Projection-Aware Candidate Extraction (BBox + Exact Intersection)] 
        │
        ▼
[5. Projected Geometry & Metric Area Computation (Auto-UTM)] 
        │
        ▼
[6. PV Scenario Formulation (A_μ,i = μ·A_i; P_i = κ_P·A_μ,i; E_i = P_i·Y_ref)] 
        │
        ▼
[7. Regional Attribution & Aggregation (Unique vs. Occurrence Tracking)] 
        │
        ▼
[8. Standardized Tables, Figures & GIS Outputs] 
```

---

## Repository Structure

```text
geoparquet-rooftop-pv-screening/
├── config/
│   └── scenario_params.yaml          # Scenario constants (κ_P, Y_ref, μ, CRS rules)
├── data/
│   ├── README.md                     # Instructions to download source datasets
│   ├── sample/                       # Lightweight sample tiles & AOIs for quick testing
│   ├── raw/                          # Raw Microsoft GeoJSON files (gitignored)
│   └── processed/                    # Spatially partitioned GeoParquet files (gitignored)
├── src/
│   ├── 01_ingest_and_validate.py     # Deduplicates source files & validates geometries
│   ├── 02_geojson_to_geoparquet.py   # Converts GeoJSON to index-free GeoParquet
│   ├── 03_spatial_tiling_index.py    # Partitions data into spatial processing tiles
│   ├── 04_projection_extraction.py   # Auto-UTM selection, bbox filter & intersection
│   ├── 05_pv_scenario_model.py       # Geometric baseline & sensitivity calculations
│   └── 06_regional_aggregation.py    # AOI aggregation & attribution excess accounting
├── notebooks/
│   ├── 01_storage_benchmark.ipynb    # Reproduces storage efficiency metrics
│   └── 02_results_and_figures.ipynb  # Reproduces division tables & sensitivity plots
├── outputs/
│   ├── tables/                       # Summary CSVs (Storage, Division results, Sensitivity)
│   └── figures/                      # High-resolution workflow & regional charts
├── CITATION.cff                      # Citation metadata
├── environment.yml                   # Conda environment specification
├── LICENSE                           # MIT License
├── README.md                         # Project documentation
└── requirements.txt                  # Python package dependencies
```

---

## Key Results (Bangladesh Case Study)

### 1. Storage Efficiency
| Format | Storage Size | File Count | Persistent Storage Reduction |
| :--- | :---: | :---: | :---: |
| **Source GeoJSON** (Line-delimited) | 6.85 GB | 49 | — |
| **Processed GeoParquet** (Columnar) | 1.53 GB | 53 | **77.7%** |

### 2. Division-Wise Results Under Maximum Geometric Envelope ($\mu = 1.00$)
*Parameters: Nominal capacity density $\kappa_P = 0.10\text{ kWp/m}^2$; Country-average reference yield $Y_{ref} = 1419.85\text{ kWh/kWp/year}$.*

| Division | Footprint Occurrences | Footprint Area ($10^6\text{ m}^2$) | Nominal PV Capacity (GWp) | Reference Energy (TWh/yr) |
| :--- | :---: | :---: | :---: | :---: |
| **Dhaka** | 4,691,397 | 459.58 | 45.96 | 65.25 |
| **Chattogram** | 3,150,203 | 295.54 | 29.55 | 41.96 |
| **Khulna** | 2,607,117 | 190.84 | 19.08 | 27.10 |
| **Rajshahi** | 2,255,307 | 195.29 | 19.53 | 27.73 |
| **Rangpur** | 2,010,444 | 139.72 | 13.97 | 19.84 |
| **Mymensingh** | 1,768,969 | 124.43 | 12.44 | 17.67 |
| **Sylhet** | 1,276,041 | 121.61 | 12.16 | 17.27 |
| **Barisal** | 1,018,439 | 81.85 | 8.18 | 11.62 |
| **Cumulative Regional Output** | **18,777,917** | **1,609.00** | **160.89** | **228.43** |

### 3. Utilization Factor Sensitivity ($\mu$)
| Utilization Factor ($\mu$) | Scenario Interpretation | Nominal Capacity (GWp) | Reference Energy (TWh/yr) |
| :--- | :--- | :---: | :---: |
| **1.00** | Maximum Geometric Envelope | 160.89 | 228.43 |
| **0.60** | Realistic utilization scenario | 96.53 | 137.06 |
| **0.30** | Conservative utilization scenario | 48.27 | 68.53 |

---

## Installation & Quickstart

### Prerequisites
The framework is built in Python using `geopandas`, `shapely`, `pyproj`, `pandas`, `numpy`, and `pyarrow`.

```bash
# 1. Clone the repository
git clone [https://github.com/SamiurRahmanAlif/geoparquet-rooftop-pv-screening.git](https://github.com/SamiurRahmanAlif/geoparquet-rooftop-pv-screening.git)
cd geoparquet-rooftop-pv-screening

# 2. Set up the Conda environment (recommended for GDAL/GEOS/PROJ dependencies)
conda env create -f environment.yml
conda activate geoparquet-pv

# Or install via pip
pip install -r requirements.txt
```

### Running on Sample Data
```bash
python src/04_projection_extraction.py --config config/scenario_params.yaml --sample
python src/05_pv_scenario_model.py --config config/scenario_params.yaml --sample
python src/06_regional_aggregation.py --config config/scenario_params.yaml --sample
```

### Running the Full National Pipeline
1. Download the Bangladesh building-footprint GeoJSON tiles into `data/raw/` (see `data/README.md`).
2. Place the administrative boundary polygons (8 divisions / 64 districts) in `data/raw/boundaries/`.
3. Execute the sequential pipeline:
```bash
python src/01_ingest_and_validate.py --input data/raw/
python src/02_geojson_to_geoparquet.py --input data/raw/ --output data/processed/
python src/03_spatial_tiling_index.py --input data/processed/
python src/04_projection_extraction.py --config config/scenario_params.yaml
python src/05_pv_scenario_model.py --config config/scenario_params.yaml
python src/06_regional_aggregation.py --config config/scenario_params.yaml
```

---

## Data Sources & Attribution

* **Building Footprints:** [Microsoft Global ML Building Footprints](https://github.com/microsoft/GlobalMLBuildingFootprints) (`release 2025-02-25`, commit `f1b082e`, accessed May 8, 2025), distributed under the **Community Data License Agreement - Permissive, Version 2.0 (CDLA-Permissive-2.0)**.
* **Solar Resource Parameters:** [Global Solar Atlas (World Bank Group & Solargis)](https://globalsolaratlas.info/) — Bangladesh country-average yield $Y_{ref} = 1419.85\text{ kWh/kWp/year}$ (`3.89 kWh/kWp/day`; screening envelope `3.68–4.23 kWh/kWp/day`).

---

## Authors & Citation

**Authors:**
* **Samiur Rahman Alif** — Dept. of ECE, North South University (`samiur.alif@northsouth.edu`)
* **Md Muslim Uddin Shuvo** — Westcliff University (`mdmuslimuddinshuvo@gmail.com`)
* **Ahmed Faizul Haque Dhrubo\*** *(Corresponding Author)* — Dept. of ECE, North South University (`ahmed.dhrubo@northsouth.edu`)
* **ASM Jahid Hasan** — Dept. of ECE, North South University (`jahid.hasan12@northsouth.edu`)
* **Mohammad Abdul Qayum** — Dept. of ECE, North South University (`mohammad.qayum@northsouth.edu`)
* **Mohammad Ashrafuzzaman Khan** — Dept. of ECE, North South University (`mohammad.khan02@northsouth.edu`)
* **Mohsin Sajjad** — Dept. of ECE, North South University (`mohsin.sajjad@northsouth.edu`)

If you use this codebase or architectural workflow in your research, please cite:

```bibtex
@article{alif2026geoparquet_pv,
  title   = {Large-Scale Geospatial Data Processing for Rooftop PV Screening Using GeoParquet: Spatial Attribution and National Building-Footprint Analysis},
  author  = {Alif, Samiur Rahman and Shuvo, Md Muslim Uddin and Dhrubo, Ahmed Faizul Haque and Hasan, ASM Jahid and Qayum, Mohammad Abdul and Khan, Mohammad Ashrafuzzaman and Sajjad, Mohsin},
  year    = {2026}
}
```

---

## License

The source code in this repository is released under the **MIT License** (see [LICENSE](LICENSE)). Underlying third-party geospatial datasets remain subject to their respective original licenses (`CDLA-Permissive-2.0` for Microsoft building footprints).
