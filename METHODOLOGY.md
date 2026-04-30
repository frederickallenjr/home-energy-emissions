# Methodology

## Overview

This project calculates household greenhouse gas emissions from metered utility consumption. The methodology follows GHG Protocol household guidance and is designed to be transparent, reproducible, and aligned with recognized reporting standards.

---

## Scope Classification

Emissions sources are classified per GHG Protocol definitions:

- **Scope 1 — Direct emissions:** Sources where combustion occurs on the property under the occupant's control. Residential natural gas qualifies because the occupant's appliances (furnace, range, dryer) perform the combustion.
- **Scope 2 — Indirect emissions:** Sources where energy is purchased but combustion occurs upstream at the point of generation. Purchased electricity qualifies because the occupant draws current; the utility bears the combustion.

This distinction is not about storage or delivery mechanism. A gas furnace and a personal vehicle are both Scope 1 for the same reason: the occupant is the emitting entity.

---

## Emissions Factors

### Natural Gas — Scope 1

**Factor:** 0.0053 metric tons CO₂ per therm

**Source:** EPA 40 CFR Part 98, Subpart C, Table C-1 — Default CO₂ Emission Factors for Various Types of Fuel. Regulatory basis for the EPA Mandatory Greenhouse Gas Reporting Rule.

**Supporting reference:** EPA Greenhouse Gas Equivalencies Calculator, https://www.epa.gov/energy/greenhouse-gas-equivalencies-calculator-calculations-and-references

**GHG coverage:** CO₂ only. CH₄ and N₂O emissions from residential natural gas combustion are negligible at household scale and excluded per standard practice. This is a documented methodological choice, not an omission.

**Rationale for factor selection:** The EPA mandatory reporting factor is the most defensible default for residential natural gas in the United States. It is statutory, widely cited, and consistent with GHG Protocol household guidance. No Oregon-specific or NW Natural-specific factor was available that would improve precision at this data volume.

---

### Electricity — Scope 2

**Factor:** 635.3 lb CO₂e / MWh (NWPP subregion total output emission rate)

**Converted to:** 0.000288 metric tons CO₂e per kWh

**Conversion:** 635.3 ÷ 2,204.6 lb/metric ton ÷ 1,000 kWh/MWh = 0.000288 tCO₂e/kWh

**Source:** EPA eGRID 2023, Subregion Summary Tables (Revision 2, released June 12, 2025), NWPP (WECC Northwest) subregion.

**Direct URL:** https://www.epa.gov/system/files/documents/2025-06/summary_tables_rev2.pdf

**eGRID subregion:** NWPP (WECC Northwest). Portland General Electric's service territory in Oregon falls within this subregion. Subregion assignment verified via EPA Power Profiler.

**GHG coverage:** CO₂e — includes CO₂, CH₄, and N₂O weighted by global warming potential (GWP) values from IPCC Fifth Assessment Report (AR5). GWP values: CO₂ = 1, CH₄ = 28, N₂O = 265.

**Rate type:** Total output emission rate. EPA recommends this rate for calculating emissions from electricity purchases, as it represents the average emissions intensity of all generation in the subregion. Nonbaseload rates are appropriate for avoided emissions calculations (e.g., efficiency or renewable projects) and are not used here.

**Rationale for static vs. dynamic factor:** Dynamic or marginal emissions factors (e.g., WattTime, Electricity Maps) would increase precision by reflecting real-time grid intensity. At one data point per billing period, the added complexity is not warranted. Static annual average factors are standard practice for household and SME carbon accounting at this scale.

**Rationale for subregion vs. state factor:** eGRID publishes both subregion and state-level factors. The NWPP subregion factor is used in preference to Oregon's state factor because PGE's grid operations are managed at the subregion level, making it the more accurate representation of the emissions intensity of delivered electricity.

---

## Factor Versioning

Emissions factors are recorded per row in the working workbook at the time of data entry. When updated factors are released, new billing periods receive the updated factor. Historical rows are not modified. This preserves methodological integrity across the full dataset and allows year-over-year comparisons to reflect the factor in effect at the time.

Factor updates are logged in `CHANGELOG.md` with the effective date and source citation.

EPA eGRID is published annually. The next expected release is eGRID 2024, anticipated in early 2026.

---

## Billing Cycle Convention

Utility billing periods do not align with calendar months. The following conventions are applied consistently:

- **month_key** is derived from the billing end date, formatted YYYY-MM, and stored as the first of that month (YYYY-MM-01) for Tableau compatibility.
- Where a billing period spans two calendar months, consumption is attributed to the month containing the billing end date.
- **Exception — December 2025 / January 2026:** A PGE billing error created a gap from January 16 through February 5, 2026. The original billing period (December 17, 2025 – January 15, 2026) was split proportionally by day count: 15 days attributed to December 2025, 15 days attributed to January 2026. Each period received half the total billed consumption (144.5 kWh). This is documented as a known data artifact in `LIMITATIONS.md`.

---

## Unit Conversion and Formula

All emissions are expressed in metric tons CO₂e (tCO₂e) to align with GHG Protocol, TCFD, and TNFD reporting conventions.

**Formula applied per row in source sheets:**

```
co2e_metric_tons = usage_value × emissions_factor
```

Where `emissions_factor` is expressed in tCO₂e per native usage unit (tCO₂ per therm for natural gas; tCO₂e per kWh for electricity). Unit conversion from published factor to workbook factor is performed once at factor entry and documented above.

**Monthly Footprint aggregation:**

```
co2e_metric_tons_total = co2e_metric_tons_gas + co2e_metric_tons_electric
```

Source sheet values are joined to the Monthly Footprint sheet on month_key using SUMIF.

---

## Scaled Architecture Note

The current Excel-based model is appropriate for single-location, low-volume personal data — one data point per billing period, two sources, indefinite but slow accumulation. A SQL + dbt architecture would be warranted under the following conditions:

- Multiple properties or locations requiring consolidated reporting
- Additional data sources materially increasing row volume
- Team-based access or automated ingestion requiring version-controlled transformation logic

In that context, the staging and mart pattern used in this workbook maps directly to dbt's model layer structure: source sheets become staging models, Monthly Footprint becomes a mart. The transition would require no reconceptualization of the data model — only a change in tooling.
