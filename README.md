# NAS drive dataset

An original dataset of NAS and enterprise hard drives: full specs, **CMR vs SMR classification**, and **annualized failure rates** derived from Backblaze Drive Stats. 153 drives.

The core of this dataset - the CMR/SMR classification and drive specs - is our own original research, compiled from manufacturer datasheets and model-number analysis. Manufacturers do not publish a unified CMR/SMR list; we built one. The failure-rate column enriches it with rates derived from Backblaze's raw Drive Stats data (our computation, their underlying counts).

Maintained at [nasdisks.com](https://www.nasdisks.com/), where the same data powers a NAS drive comparison across seven Amazon regions.

> **These files are a dated snapshot and may be out of date.** The current, always-up-to-date dataset lives at **[nasdisks.com/data/](https://www.nasdisks.com/data/)** - download the latest there, or call the CORS-enabled endpoints below. Each file in `data/` carries its export date in the filename so you always know how old your copy is.

## What's inside

| File | Rows | What it is |
|------|------|------------|
| [`data/drives-2026-06-21.csv`](data/drives-2026-06-21.csv) | 153 | The headline file: one flat row per drive, all specs and failure rate combined. |
| [`data/drives-2026-06-21.json`](data/drives-2026-06-21.json) | 153 | The same rows as JSON (mirror of the live API). |
| [`data/cmr-reference-2026-06-21.csv`](data/cmr-reference-2026-06-21.csv) | 149 | Source of truth for specs and CMR/SMR classification (including idle/seek noise and helium fill), with a per-row `confidence` flag. Our original research. |
| [`data/backblaze-afr-2026-06-21.csv`](data/backblaze-afr-2026-06-21.csv) | 36 | Full-year 2025 annualized failure rates, computed from Backblaze raw Drive Stats. |
| [`data/series-reliability-2026-06-21.csv`](data/series-reliability-2026-06-21.csv) | - | Manufacturer series specs (warranty, rated workload, MTBF) used as context where Backblaze has no data. |

## Quick start

The files in `data/` are ready to use as-is. If you'd rather hit a live endpoint (CORS-enabled, cached, no key, no rate limit):

```bash
# JSON: { source, license, attribution, count, drives: [...] }
curl -s https://www.nasdisks.com/data/drives.json | jq '.drives[0]'

# Flat CSV
curl -s https://www.nasdisks.com/data/drives.csv
```

## Columns (`drives-2026-06-21.csv` / `drives-2026-06-21.json`)

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
| `acoustic_idle_db` / `acoustic_seek_db` | Idle / seek noise as A-weighted sound power (dB), from manufacturer datasheets. Null where the maker doesn't publish it. |
| `is_helium` | Whether the drive is helium-sealed (`true`/`false`); null if unconfirmed. |
| `drive_class` | NAS, NAS-Pro, Enterprise, Surveillance, Desktop, etc. |
| `in_production` | Whether the drive is currently sold new. |
| `also_sold_as` | Equivalent SKUs merged into this drive - format/firmware/secure-erase/OEM variants of the same physical drive. `;`-separated in the CSV, an array in JSON; empty when none. |
| `afr_pct` | Annualized failure rate, full-year 2025, derived from Backblaze Drive Stats (null where Backblaze doesn't track the model). |
| `reliability_drive_count` / `reliability_drive_days` / `reliability_failures` | The raw Backblaze figures behind `afr_pct`. |
| `reliability_source` | Provenance of the failure rate. |

## Methodology

- **CMR vs SMR** is our original classification, built from manufacturer datasheets and model-number decoding. Manufacturers ship SMR drives into NAS lines without labelling them as such; this dataset exists because there was no single authoritative per-model reference. The `confidence` column in `cmr-reference-2026-06-21.csv` flags certainty per row.
- **Noise and helium** are read from manufacturer datasheets (idle/seek A-weighted sound power; helium-seal designation). Our compilation; licensed CC BY 4.0 alongside the other specs.
- **Failure rates** are computed from Backblaze's raw 2025 quarterly Drive Stats: we aggregate drive-days and failure counts across quarters into a single full-year annualized rate and validate against Backblaze's published fleet figure (~1.36% for 2025). The computation and methodology are ours; the underlying counts are Backblaze's (see licence section). Models Backblaze doesn't track are left null rather than guessed.
- **`series-reliability-2026-06-21.csv`** gives manufacturer-rated context (warranty, workload TB/year, MTBF) for lines Backblaze doesn't run.

## Licence & attribution

**Short version: the specs and CMR/SMR data are fully open (CC BY 4.0). The failure-rate fields are free to use but must credit Backblaze and cannot be resold on their own.**

### 1. Drive specs, CMR/SMR classification, and acoustics - CC BY 4.0

Our original work (every field *except* the failure-rate ones). Licensed under [Creative Commons Attribution 4.0 (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/); full text in [`LICENSE`](LICENSE).

- **You can:** copy, modify, redistribute and build on it - including commercially.
- **You must:** credit the source, e.g.
  ```
  Drive specs & CMR/SMR by nasdisks.com, CC BY 4.0
  ```
  A link back to https://www.nasdisks.com/ is enough.

### 2. Failure-rate data - Backblaze's terms (NOT CC BY 4.0)

The fields `afr_pct`, `reliability_drive_count`, `reliability_drive_days`, `reliability_failures` (in `drives-2026-06-21.csv` / `drives-2026-06-21.json`) and all of `backblaze-afr-2026-06-21.csv` are derived from Backblaze Drive Stats. The computation is ours; the underlying counts are Backblaze's.

- **You can:** use it for free, and build (and even sell) derivative works from it.
- **You must:** credit Backblaze as the source.
- **You can't:** sell the raw data itself.
- Backblaze does not endorse this project. Details in [`NOTICE`](NOTICE).

## Updates

This repo is a point-in-time snapshot; the date in each filename is its export date. For current data use [nasdisks.com/data/](https://www.nasdisks.com/data/) or the endpoints above.

## Changelog

- **2026-06-21** - Added per-drive noise (idle / seek A-weighted sound power, dB) and helium-fill fields. Corrected WD40EZAX / WD60EZAX to CMR (the newer EZAX revision is CMR; only the older EZAZ is SMR).
- **2026-06-16** - Initial public dataset: full specs, CMR/SMR classification, and full-year 2025 Backblaze annualized failure rates.
