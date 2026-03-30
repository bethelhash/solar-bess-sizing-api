# Solar + BESS Sizing & Dispatch Optimization API

**Commercial and industrial battery storage sizing in a single API call.**  
NREL ATB 2024 CAPEX · PNNL LCOS · IRA 2022 §48E ITC · Every figure cited to primary source.

[![RapidAPI](https://img.shields.io/badge/RapidAPI-Subscribe-0055DA?style=flat-square)](https://rapidapi.com/bethelnedi/api/solar-bess-sizing-api)
[![Demo](https://img.shields.io/badge/Live_Demo-solartruth.app-FFD21E?style=flat-square)](https://bessiq.vercel.app/))
[![Hosted](https://img.shields.io/badge/Hosted_on-Hugging_Face-FFD21E?style=flat-square)](https://amfumu-solar-bess-api.hf.space/docs)

---

## The Problem This Solves

Commercial and industrial building owners face peak demand charges averaging **$13–$22/kW/month**. A correctly sized BESS can shave 15–25% of peak demand, but most sizing tools either use flat $/kWh rules that underestimate cost, or opaque black-box models that banks won't underwrite.

This API implements the **NREL Load Duration Curve method** (Neubauer & Simpson 2015) and the **ATB 2024 two-component CAPEX model** — the same methodology used in NREL technical reports. Every output is traceable to a named source document.

---

## Quick Start

```bash
curl -X POST https://solar-bess-sizing-api.p.rapidapi.com/bess/quick \
  -H "X-RapidAPI-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "peak_demand_kw": 500,
    "state": "CA",
    "flat_rate_usd_kwh": 0.18,
    "chemistry_type": "lfp",
    "discharge_hours": 4.0
  }'
```

**Returns:** system size (kW/kWh), CAPEX low/mid/high, LCOS, ITC credit, annual savings breakdown, viability verdict.

---

## Endpoints

| Method | Endpoint | Plan | Description |
|--------|----------|------|-------------|
| `POST` | `/bess/quick` | Free | Minimal-input sizing — peak demand, state, rate |
| `POST` | `/bess/optimal` | Pro | Full NPV/IRR, ITC adder stacking, all 3 ATB scenarios |
| `POST` | `/bess/solar-plus` | Pro | Co-optimised solar + BESS sizing |
| `GET` | `/reference/states` | Free | Demand charge rates by state (OpenEI 2024) |
| `GET` | `/reference/chemistry` | Free | Battery chemistry database with RTE values |
| `GET` | `/reference/methodology` | Free | Full citation register |

---

## Methodology

### CAPEX Model — NREL ATB 2024
```
Total Cost ($/kW) = [BatteryPack ($/kWh) × Duration (h)] + BOS ($/kW)
```
Separates energy cost from power cost. Eliminates incorrect flat $/kWh assumptions.

| Scenario | Pack Cost | BOS Cost | Source |
|----------|-----------|----------|--------|
| Conservative | $283/kWh | $371/kW | Ramasamy et al. 2023 via ATB2024 |
| Moderate | $241/kWh | $339/kW | Cole, Ramasamy & Turan 2025 NREL/TP-6A40-93281 |
| Advanced | $189/kWh | $290/kW | Cole et al. 2025 low trajectory |

### LCOS — PNNL-33283
```
LCOS = (CAPEX + NPV of O&M) / NPV of energy throughput
```
Fixed O&M: 2.5%/yr · Discount rate: WACC (default 7%) · Includes degradation.  
Validated range LFP: **$200–$400/MWh** [PNNL-33283 §4.2]

### Sizing — Load Duration Curve
Power sized by peak shave target percentile [Neubauer & Simpson 2015 NREL/TP-5400-63162].  
Energy: `E = P_bess × discharge_hours / DoD` [Ashabi et al. 2022 IJE Transactions B 35(8)]

### ITC — IRA 2022 §48E
| Component | Rate | Condition |
|-----------|------|-----------|
| Base | 30% | All standalone BESS from Jan 1 2023 |
| Domestic content | +10% | IRA 2022 §48C(e)(2)(A) |
| Energy community | +10% | IRA 2022 §48E(b)(3)(B) |
| **Maximum** | **50%** | All adders stacked |

### Round-Trip Efficiency Convention
All RTE values use **AC-AC convention** (grid meter boundary):
- LFP: 0.85 · NMC: 0.87 · LFP Long-Duration: 0.84

> Manufacturer DC-DC figures (0.92–0.95) are not comparable. We use AC-AC because commercial customers are billed on metered AC output.

---

## Plans

| Plan | Price | Calls | Endpoints |
|------|-------|-------|-----------|
| Free | $0/mo | 5/min | `/bess/quick` + reference |
| Pro | $99/mo | 1,000/hr | All endpoints |
| Ultra | $299/mo | 1,000/hr | All + white-label |

[Subscribe on RapidAPI →]((https://rapidapi.com/bethelnedi/api/solar-bess-sizing-dispatch-optimization-api))

---

## Full Citation Register

- Cole & Karmakar (2023) NREL/TP-6A40-85332
- Cole, Ramasamy & Turan (2025) NREL/TP-6A40-93281
- Ramasamy et al. (2023) NREL/TP-7A40-87610
- Mongird et al. (2022) PNNL-33283 — Grid Energy Storage Cost & Performance Assessment
- Neubauer & Simpson (2015) NREL/TP-5400-63162
- Ashabi et al. (2022) IJE Transactions B 35(8):1662–1673
- Mayhorn et al. (2022) PNNL — Battery Energy Management System
- Jordan & Kurtz (2013) Progress in Photovoltaics 21(8):1139–1150
- IRA 2022 §48E — Standalone BESS Investment Tax Credit
- OpenEI Utility Rate Database (2024)
- Luthander et al. (2015) Renewable Energy 76:142–150

---

## Topics

`bess` `battery-storage` `solar-bess` `demand-charge` `lcos` `nrel-atb` `ira-2022` `investment-tax-credit` `commercial-solar` `energy-storage` `fastapi` `python` `rapidapi` `solar-api` `pnnl` `lithium-iron-phosphate` `lfp` `nmc` `peak-shaving` `tou-arbitrage`
