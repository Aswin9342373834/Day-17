# Day 17 — Vehicle Cost Analysis Dashboard

A single-file HTML dashboard that compares running costs, CO₂ output, maintenance, and refuel time across five fuel types (Petrol, Diesel, CNG, E85, EV), built from a trip-level CSV dataset.

## Files

| File | Description |
|---|---|
| `vehicle_cost_dashboard.html` | The dashboard. Pure HTML/CSS/SVG/JS — no CDN, no build step. Open directly in any browser. |
| `vehicle_cost_data.csv` | Sample dataset: 727 simulated trips across 5 fuel types and car ages 0–12 years. |

## My vehicle inputs

| Field | Value |
|---|---|
| Vehicle | Honda City |
| Fuel | Petrol |
| Usage | City |
| Distance | 1,500 km/month |
| Car age | 7 years (Aged: 6–9y bucket) |

> Note: I didn't have a real CSV on hand, so this run uses a **generated sample dataset** with realistic Indian fuel prices, mileage, and maintenance-aging curves — not actual logged trips. Swap in your own CSV (same column schema, see below) to get personalized numbers.

## CSV schema

```
Trip_ID, Fuel_Type, Car_Age_Years, Distance_km,
Fuel_Cost_INR, CO2_emitted_kg, Maintenance_Cost_INR, Refuel_Recharge_time_min
```

## Metrics computed

- **Cost/km, CO₂/km, Maintenance/km** — aggregated per fuel type (sum of cost ÷ sum of distance, not row-wise average, to avoid bias from trip length)
- **Age-bucket costs** — New (0–2y) / Mid-life (3–5y) / Aged (6–9y) / Old (10+y)
- **Cost/km vs age curve** — 0 to 12 years, per fuel
- **E85 Paradox**:
  - Pump saving = (Petrol price − E85 price) / Petrol price × 100
  - Running penalty = (E85 cost/km − Petrol cost/km) / Petrol cost/km × 100
  - Break-even price = (E85 mileage ÷ Petrol mileage) × Petrol price
- **E85 Composite Score (/10)** — weighted: Cost 4pt, CO₂ 3pt, Refuel 2pt, Maintenance 1pt, each scored relative to the best-performing fuel on that metric

## Key findings (sample data)

| Fuel | Cost/km | CO₂/km | Maint/km | Refuel |
|---|---|---|---|---|
| EV | ₹1.55 | 0.144 kg | ₹0.09 | 47.0 min |
| CNG | ₹3.40 | 0.070 kg | ₹0.22 | 8.3 min |
| Diesel | ₹4.79 | 0.138 kg | ₹0.33 | 5.3 min |
| **Petrol (yours)** | **₹6.51** | **0.144 kg** | **₹0.27** | **5.3 min** |
| E85 | ₹7.59 | 0.116 kg | ₹0.27 | 6.4 min |

- **Your monthly fuel cost**: ₹9,767 at 1,500 km/month on Petrol.
- **The E85 Paradox holds up**: E85 is 14.98% cheaper *at the pump*, but its lower mileage means real cost/km ends up **16.6% higher** than Petrol. Pump price would need to drop to **₹75.27/L** (from ₹88.00/L) to break even with Petrol on a per-km basis.
- **E85 Composite Score: 4.63/10** — middling. It loses heavily on the cost component (only 0.82/4) despite scoring fine on CO₂ (1.82/3) and refuel time (1.66/2).
- **EV is the standout** on both cost/km and maintenance/km, but the ~47-minute refuel/recharge time is the clear trade-off versus the ~5–8 minutes for liquid/gas fuels.
- **Maintenance cost climbs steeply with age** across all fuels — Petrol goes from ₹0.11/km (New) to ₹0.43/km (Old), roughly a 4x increase by the 10+ year mark.

## How to use

1. Open `vehicle_cost_dashboard.html` in any modern browser — works offline, no dependencies.
2. Hover over the bar chart, doughnut chart, and line chart for exact tooltip values.
3. To re-run with your own data: replace `vehicle_cost_data.csv` with your real trip log (same columns), update the `[YOUR VEHICLE]`-style fields, and regenerate.

## Learnings

- Aggregating cost/km as **(sum of cost) / (sum of distance)** rather than averaging per-trip ratios avoids over-weighting short trips with noisy fuel-cost variance.
- The "obvious" cheaper option at the pump (E85) is not the cheaper option per km once mileage is factored in — this is the kind of insight a single dashboard view should surface upfront rather than bury in a table.
- Pure SVG charts (no chart library, no CDN) keep the file fully self-contained and portable, at the cost of more manual layout/scaling math for axes and tooltips.
