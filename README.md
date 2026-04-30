# Home Energy & Carbon Accounting

**Author:** Rick Allen
**Location:** Portland, OR
**Data Start:** December 17, 2025
**Last Updated:** April 2026

---

## Project Purpose

This project tracks and quantifies the household carbon footprint associated with residential energy consumption. It is designed as a personal monitoring tool and as a demonstration of end-to-end analytics workflow applied to sustainability data — from raw utility records through emissions calculation to visual reporting.

Scope is intentionally constrained to align with recognized household GHG reporting standards. The GHG Protocol and most residential carbon accounting frameworks — including EPA's household calculator and ENERGY STAR — treat metered utility consumption (natural gas and purchased electricity) as the primary and most defensible basis for household emissions reporting. Mileage-based and other self-reported sources are excluded from this scope not as an oversight, but as a deliberate methodological alignment with those standards. Decisions about what to include, exclude, and defer are documented explicitly.

**Emissions sources currently in scope:**
- Residential natural gas combustion (Scope 1)
- Purchased electricity (Scope 2)

---

## Data Sources

| Source | Utility | Scope | Unit | Coverage |
|---|---|---|---|---|
| Portland General Electric | PGE | Scope 2 | kWh | Dec 2025 – present |
| Northwest Natural | NW Natural | Scope 1 | therms | Dec 2025 – present |

Raw source files are exported via Green Button Download and export data is incorporated into the master workbook; original files are retained locally but excluded from the repository due to personally identifiable information in the utility export format. The master workbook (home_energy_emissions.xlsx) is preserved in `/data`. All transformations occur in the working workbook and are documented in `METHODOLOGY.md`.

**Note on billing cycles:** Utility billing periods do not align with calendar months. A `month_key` field (YYYY-MM) derived from the billing end date is used as the join key across all sources. This convention is applied consistently and documented per entry.

---

## Emissions Methodology

Carbon dioxide equivalent (CO₂e) is calculated by multiplying metered consumption by a static annual average emissions factor. Results are expressed exclusively in **metric tons CO₂e (tCO₂e)** to align with GHG Protocol, TCFD, and TNFD reporting conventions.

| Source | Scope | Emissions Factor | Factor Source |
|---|---|---|---|
| Natural gas | Scope 1 | 53 kg CO₂ / MMBtu (≈ 0.0053 tCO₂ / therm) | EPA 40 CFR Part 98, Table C-1 |
| Electricity (NWPP) | Scope 2 | 635.3 lb CO₂e / MWh | EPA eGRID 2023, NWPP subregion |

Emissions factors are recorded per row in the working workbook. When updated factors are released — EPA eGRID is published annually — new billing periods receive the updated factor while historical rows remain unchanged, preserving methodological integrity across the full dataset. Factor changes are logged in `CHANGELOG.md`.

Factor selection rationale, conversion arithmetic, and GWP assumptions are documented in `METHODOLOGY.md`.

---

## Workbook Structure

Transformations are performed in a single Excel workbook following a staging and mart pattern:

- **Natural_Gas** — one row per billing period; usage, cost, emissions factor, tCO₂e
- **Electric** — one row per billing period; usage, cost, emissions factor, tCO₂e
- **Monthly_Footprint** — one row per month; tCO₂e per source and total household tCO₂e

The Monthly Footprint sheet is the mart table and the primary data source for Tableau.

---

## Repository Structure

```
/
├── README.md                   # Project overview (this file)
├── METHODOLOGY.md              # Emissions factor sourcing and conversion math
├── CHANGELOG.md                # Version history and data updates
├── LIMITATIONS.md              # Known constraints and caveats
├── LICENSE
├── /data
│   ├── nwnatural_raw.xlsx      # NW Natural Green Button export, unmodified
│   └── pge_raw.xlsx            # PGE Green Button export, unmodified
└── /viz
    └── [Tableau Public link or screenshots]
```

---

## Limitations

- **Billing cycle misalignment:** Billing periods do not correspond to calendar months. The `month_key` convention described above is applied consistently but introduces minor attribution error at period boundaries.
- **Static emissions factors:** Grid emissions intensity varies in real time. Static annual average eGRID factors are standard practice for household and SME carbon accounting at this data volume but understate variability during periods of high renewable penetration or grid stress.
- **Data sparsity:** Each source produces one data point per billing period. This constrains statistical analysis and makes anomaly detection unreliable until multiple years of data are available.
- **Scope 1 / Scope 2 only:** Scope 3 emissions — embodied carbon in purchased goods, food, air travel, etc. — are outside scope and would require a fundamentally different data collection approach.
- **Natural gas factor is CO₂ only:** CH₄ and N₂O contributions from residential natural gas combustion are negligible at this scale and excluded per standard household accounting practice. This is a documented methodological choice, not an omission.

---

## Future Opportunities

- **Expanded Scope 1 sources:** Automotive fuel, marine fuel, and other direct combustion sources fall outside recognized household reporting standards but represent a legitimate extension of this methodology. Their exclusion here reflects standard practice; their inclusion in a future iteration would require documented estimation methodology and explicit framing as supplemental to, rather than part of, the core GHG Protocol-aligned scope.
- **Automated ingestion:** Both PGE and NW Natural support Green Button Download. A file-parsing script could automate transcription, workbook append, and Git commit — eliminating manual data entry as billing periods accumulate.
- **Utility API integration:** If PGE Connect My Data API access becomes available for residential accounts, automated pull-and-append could replace the download-and-parse workflow entirely.
- **Dynamic grid emissions:** Tools like WattTime or Electricity Maps provide real-time marginal emissions intensity for the NWPP region. Integration would increase precision at the cost of added pipeline complexity.
- **Scaled architecture:** The Excel-based model is appropriate for single-location, low-volume personal data. A SQL + dbt pipeline would be warranted if this methodology were applied across multiple properties or extended over a decade-plus timeframe. See `METHODOLOGY.md` for a brief on what that transition would involve.

---

## Visualization

Dashboard to be built in Tableau (expected by May 15, 2026). Panels include:

- Natural gas consumption (therms / month)
- Electricity consumption (kWh / month)
- Total household carbon footprint (tCO₂e / month)

*[Tableau Public link placeholder]*

---

*Emissions accounting follows GHG Protocol household guidance. Factor selection and conversion methodology documented in* `METHODOLOGY.md`.
