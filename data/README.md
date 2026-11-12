# Dataset Instructions

Due to GitHub file size limits, raw building-footprint files and processed GeoParquet tiles are not stored directly in this repository.

## 1. Building Footprints (Microsoft Global ML Building Footprints)
* **Source:** [Microsoft Global ML Building Footprints](https://github.com/microsoft/GlobalMLBuildingFootprints)
* **Release / Commit:** `2025-02-25` (commit `f1b082e`, accessed May 8, 2025)
* **License:** `CDLA-Permissive-2.0`
* **Setup:** Download the 49 Bangladesh line-delimited GeoJSON tiles (`6.85 GB`) and place them in `data/unzipped/geojson/` (removing the 4 duplicate file copies during ingestion as documented in Section III-A of the paper).

## 2. Administrative Boundaries
* Place the Bangladesh 8-division and 64-district boundary polygons inside `data/boundaries/`.

## 3. Pre-Processed Archive
* The complete pre-processed dataset archive (`3.10 GB`) is hosted externally on [Google Drive](https://drive.google.com/file/d/1hmwgNz8nbGm-h_sbeeCn3RTcsi6a-ncz/view?usp=sharing).
