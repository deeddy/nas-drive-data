# NAS drive dataset

An open dataset of NAS hard drives: full specs, **CMR vs SMR**, and real-world **Backblaze annualized failure rates (AFR)**. 140+ drives, with a measured failure rate for the models Backblaze tracks. Our drive specs and CMR/SMR data are free to use - including commercially - under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) with credit; the Backblaze failure-rate fields stay under [Backblaze's own terms](#licence--attribution) (cite Backblaze; don't sell the data itself).

Maintained at [nasdisks.com](https://www.nasdisks.com/), where the same data powers a NAS drive price comparison across seven Amazon regions.

> **These files are a dated snapshot and may be out of date.** The current, always-up-to-date dataset lives at **[nasdisks.com/data/](https://www.nasdisks.com/data/)** - download the latest there, or call the CORS-enabled endpoints below. Each file in `data/` carries its export date in the filename so you always know how old your copy is.

## What's inside

| File | Rows | What it is |
|------|------|------------|
| [`data/drives-2026-06-16.csv`](data/drives-2026-06-16.csv) | 140+ | The headline file: one flat row per drive, specs joined to failure rate. |
| [`data/drives-2026-06-16.json`](data/drives-2026-06-16.json) | 140+ | The same rows as JSON (mirror of the live API). |
| [`data/cmr-reference-2026-06-16.csv`](data/cmr-reference-2026-06-16.csv) | 140+ | Source of truth for specs + CMR/SMR classification, with a per-row `confidence` flag. |
| [`data/backblaze-afr-2026-06-16.csv`](data/backblaze-afr-2026-06-16.csv) | 36 | Full-year 2025 Backblaze failure rates per model (the models Backblaze runs at scale). |
| [`data/series-reliability-2026-06-16.csv`](data/series-reliability-2026-06-16.csv) | - | Manufacturer series specs (warranty, rated workload, MTBF) used as context where Backblaze has no data. |

## Quick start

The files in `data/` are ready to use as-is. If you'd rather hit a live endpoint (CORS-enabled, cached, no key, no rate limit):

```bash
# JSON: { source, license, attribution, count, drives: [...] }
curl -s https://www.nasdisks.com/data/drives.json | jq '.drives[0]'

# Flat CSV
curl -s https://www.nasdisks.com/data/drives.csv
```

## Columns (`drives-2026-06-16.csv` / `drives-2026-06-16.json`)

| Column | Meaning |
|--------|---------|
| `model` | Manufacturer model number (e.g. `WD80EFPX`). |
| `brand` | WD, Seagate, Toshiba, HGST. |
| `line` | Product line (Red Plus, IronWolf Pro, N300, Exos, Ultrastar, etc.). |
| `capacity_tb` | Capacity in TB. |
| `rpm` | Rotational speed. |
| `cache_mb` | DRAM cache in MB. |
| `interface` | SATA or SAS. |
| `form_factor` | 3.5 or 2.5. |
| `recording_tech` | `cmr` or `smr` - the field that matters most for RAID/NAS use. |
| `drive_class` | NAS, NAS-Pro, Enterprise, Surveillance, Desktop, etc. |
| `in_production` | Whether the drive is currently sold new. |
| `afr_pct` | Backblaze annualized failure rate, full-year 2025 (null where Backblaze doesn't track the model). |
| `reliability_drive_count` / `reliability_drive_days` / `reliability_failures` | The raw figures behind `afr_pct`. |
| `reliability_source` | Provenance of the failure rate. |

## Methodology

- **CMR vs SMR** is classified from manufacturer datasheets and model-number decoding. SMR drives shingle their tracks, which cripples random-write and RAID-rebuild performance, so the distinction is the single most important spec for a NAS - and manufacturers do not always advertise it. The `confidence` column in `cmr-reference-2026-06-16.csv` flags how certain each call is.
- **Failure rates** are computed from Backblaze's raw 2025 quarterly Drive Stats, aggregated to a full-year annualized rate and validated against Backblaze's own published fleet figure (~1.36% for 2025). Only the catalog models that exactly match a Backblaze-tracked model carry an `afr_pct`; the rest are left null rather than guessed.
- **`series-reliability-2026-06-16.csv`** gives manufacturer-rated context (warranty years, workload TB/year, MTBF) for lines Backblaze doesn't run, so you have *something* to reason about beyond a blank cell.

## Licence & attribution

**Short version: the specs are fully open; the failure rates are free to use but must credit Backblaze and can't be resold on their own.**

### 1. Drive specs + CMR/SMR classification - CC BY 4.0

Our own work (every field *except* the failure-rate ones). Licensed under [Creative Commons Attribution 4.0 (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/); full text in [`LICENSE`](LICENSE).

- **You can:** copy, modify, redistribute and build on it - including commercially.
- **You must:** credit the source, e.g.
  ```
  Drive specs & CMR/SMR by nasdisks.com, CC BY 4.0
  ```
  A link back to https://www.nasdisks.com/ is enough.

### 2. Failure-rate data - Backblaze's terms (NOT CC BY 4.0)

The fields `afr_pct`, `reliability_drive_count`, `reliability_drive_days`, `reliability_failures` (in `drives-2026-06-16.csv` / `drives-2026-06-16.json`) and all of `backblaze-afr-2026-06-16.csv` come from Backblaze Drive Stats.

- **You can:** use it for free, and build (and even sell) derivative works from it.
- **You must:** credit Backblaze as the source.
- **You can't:** sell the raw data itself.
- Backblaze does not endorse this project. Details in [`NOTICE`](NOTICE).

## Updates

This repo is a point-in-time snapshot; the date in each filename is its export date and it can fall behind. For current data use [nasdisks.com/data/](https://www.nasdisks.com/data/) or the endpoints above, which always reflect the latest catalogue and Backblaze figures.
