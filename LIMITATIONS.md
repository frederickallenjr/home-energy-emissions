# Limitations

## Billing Cycle Misalignment

Utility billing periods do not correspond to calendar months. A month_key derived from the billing end date is used as the join key across all sources. This introduces minor attribution error at period boundaries — consumption occurring in one calendar month may be attributed to another depending on where the billing period ends. This convention is applied consistently across all sources and all periods.

---

## PGE Billing Error — January 2026 Data Gap

A PGE billing error created a gap in electric consumption data from January 16 through February 5, 2026. The affected billing periods are:

- Original period: December 17, 2025 – January 15, 2026 (289 kWh)
- Gap: January 16, 2026 – February 5, 2026 (no data)
- Next period: February 6, 2026 – February 25, 2026 (128 kWh)

**Methodology applied:** The original 289 kWh billing period was split proportionally by day count — 15 days in December 2025, 15 days in January 2026 — resulting in 144.5 kWh attributed to each month. The gap period is unmetered and unestimated.

**Impact:** December 2025 and January 2026 electric consumption figures represent 15-day periods, not full months. Both will read low relative to subsequent full billing periods. This affects year-over-year comparisons for those months and should be accounted for in any trend analysis.

---

## Data Sparsity

Each emissions source produces one data point per billing period. At project initiation, four to five months of data are available. This constrains statistical analysis — averages are not yet stable, anomaly detection is unreliable, and seasonal patterns cannot be confirmed until multiple years of data are accumulated. Trend observations should be treated as preliminary until the dataset matures.

---

## Static Emissions Factors

The electricity emissions factor (EPA eGRID 2023, NWPP subregion) is a static annual average. Actual grid emissions intensity varies in real time depending on generation mix, demand, imports, and renewable availability. Static annual average factors are standard practice for household and SME carbon accounting at this data volume but will understate emissions during periods of high fossil generation and overstate them during periods of high renewable penetration.

Natural gas emissions factors do not vary materially by season or region for residential combustion and are not subject to this limitation.

---

## Scope Coverage

This project covers Scope 1 (natural gas) and Scope 2 (purchased electricity) emissions only, consistent with recognized household GHG reporting frameworks including GHG Protocol and EPA household guidance.

The following are explicitly outside scope:

- **Scope 3 emissions:** Embodied carbon in purchased goods, food production, air travel, waste, and other upstream and downstream activities. Quantifying Scope 3 at the household level would require a fundamentally different data collection methodology.
- **Additional Scope 1 sources:** Automotive fuel, marine fuel, propane, and other direct combustion sources are excluded. Their exclusion reflects alignment with standard household reporting practice, not a claim that they are immaterial. See `METHODOLOGY.md` and Future Opportunities in `README.md` for further context.

---

## Early Occupancy Period

The project data begins December 17, 2025, the date of home purchase. The first several months of data reflect an early occupancy period during which household energy consumption patterns were not yet stable:

- **December 2025:** Half month of data. No washer or dryer installed.
- **January 2026:** 15-day data artifact resulting from PGE billing error. No washer or dryer installed.
- **February 2026:** Primary occupant absent for majority of billing period, reducing heating and appliance load.
- **March 2026 onward:** Household fully occupied with complete appliance load.

Comparisons involving December 2025 through February 2026 should account for these conditions.

---

## Natural Gas Factor — CO₂ Only

The natural gas emissions factor covers CO₂ emissions only. CH₄ (methane) and N₂O (nitrous oxide) contributions from residential natural gas combustion exist but are negligible at household scale. Their exclusion is consistent with standard household carbon accounting practice and is a documented methodological choice, not an omission. Full CO₂e accounting for natural gas would require applying GWP-weighted CH₄ and N₂O factors from EPA 40 CFR Part 98, Table C-2.
