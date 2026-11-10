# Model assumptions and decisions

**Status:** working family comparison, reviewed 7 November 2026.  
**Source of truth for executable defaults:** `index.html`, specifically the `defaults` JavaScript object and matching input `value` attributes. This document explains why those values were chosen; if code and documentation disagree, investigate and reconcile them.

## Objective and scope

Estimate the **equivalent monthly economic cost of owning and using a car** over a five-year period. Compare one conventional petrol car with one **battery-financed electric vehicle (EV)**, assuming non-financial attributes such as comfort, styling and driving experience are equally satisfactory. The comparison is intended for a first-time car buyer in Kolkata, West Bengal. Neither column identifies a binding vehicle quotation.

The result includes the cost of tying up capital at an assumed annual 6% opportunity-cost rate. It is **not** the monthly amount leaving the bank account. All model inputs are editable.

## Current default inputs

| Model input (code key) | Petrol | Battery-financed EV | Units / interpretation |
|---|---:|---:|---|
| Upfront vehicle price (`pCost` / `eCost`) | ₹12,00,000 | **₹8,00,000** | Rupees, on-road **assumptions**; first-year insurance included. **EV figure excludes the separately financed battery.** |
| Daily distance (`dailyKm`) | 40 | 40 | km per calendar day, shared |
| Petrol efficiency (`pMileage`) | 10 | — | km per litre, real-world assumption |
| Petrol price (`pFuel`) | ₹114 | — | ₹ per litre |
| EV electricity consumption (`eEfficiency`) | — | 0.20 | kWh from wall meter per km; charging losses already included |
| Electricity cost (`eTariff`) | — | ₹11 | ₹ per kWh, flat **marginal / incremental** all-in estimate for home charging |
| Home charger + installation (`eCharger`) | ₹0 | ₹75,000 | Rupees, additional one-time upfront cost |
| Insurance renewal (`pInsurance` / `eInsurance`) | ₹15,000 | ₹15,000 | ₹ per year, each of **Years 2–5** in a 5-year case |
| Maintenance (`pMaintenance` / `eMaintenance`) | ₹10,000 | ₹5,000 | ₹ per year; routine service estimate, not major repairs |
| Driver salary (`driver`) | ₹18,000 | ₹18,000 | ₹ per month, shared |
| Gross resale proceeds (`pResale` / `eResale`) | ₹5,00,000 | ₹5,00,000 | Rupees at end of Year 5; EV resale assumed to include battery |
| Holding period (`years`) | 5 | 5 | Years = 60 model months, shared |
| Opportunity-cost rate (`discount`) | 6% | 6% | **Effective annual** rate, shared; not a lending rate |
| Petrol, electricity, driver, maintenance, insurance annual escalation (`gFuel`, `gElec`, `gDriver`, `gService`, `gInsurance`) | 0% | 0% | Annual increase; individually editable |

### EV battery-financing assumptions

| Input | Default | Meaning |
|---|---:|---|
| Battery loan principal (`loanPrincipal`) | ₹5,50,000 | Separately financed; **not** included in ₹8L vehicle purchase |
| Nominal annual loan interest (`loanAPR`) | 9.5% | Reducing balance, paid monthly |
| Loan term (`loanMonths`) | 60 months | Equal to default five-year car-holding period |
| Calculated battery loan EMI | ~₹11,551/month | Derived from the above, **not** Kia's advertised ₹3.30/km |
| Loan outstanding at sale under defaults | ₹0 | Because all 60 EMIs have been made; recalculates if term/horizon changes |
| Upfront battery down payment / processing fees | ₹0 modelled | Not established; must be added if quotations show them |

**Important:** The battery figures are deliberately assumed, not confirmed by Kia or a lender. Kia's advertised **₹3.30/km** is an illustrative loan-EMI equivalent based on **2,200 km/month** (₹7,260/month as an illustration). It is **not** the formula used to bill our actual 40 km/day. See [research and caveats](research-and-caveats.md).

## Derived distance and consumption at current defaults

- Distance: **40 km/day × 365 = 14,600 km/year**, or **73,000 km over five years**; model monthly mean = **1,216.67 km**.
- Petrol: **4 L/day**; **₹456/day**; **₹13,870 per average month** at ₹114/L, assuming fixed fuel price.
- EV home electricity: **8 kWh/day**; **2,920 kWh/year**; **₹2,676.67 per average month** at ₹11/kWh, assuming fixed tariff. This excludes the battery EMI.
- Battery EMI is fixed by the **assumed financing schedule**, not calculated as actual kilometres × ₹3.30.

## Decision history and what supersedes what

1. Initially, **both** cars were given an illustrative ₹12 lakh on-road price, inclusive of Year 1 insurance.
2. Insurance was deliberately simplified to ₹15,000 annually for both during renewal Years 2–5. Later, a quotation/insurance detail worksheet can feed into this single input.
3. Agreed **40 km/day** for both and **10 km/L** for petrol; ₹114/L petrol.
4. Agreed **0.20 kWh/km** for the EV including charging loss, **₹11/kWh** for CESC-based home charging, and **₹75,000** charger + installation.
5. Agreed ₹10,000/year petrol servicing, ₹5,000/year EV servicing, ₹18,000/month driver, ₹5 lakh sale proceeds for **both** after five years, **6% annual** opportunity cost, and **0% annual cost escalation**.
6. Battery pricing was initially **deferred** after uncertainty over Kia's quoted ₹3.30/km / ₹7,260/month offer. Later, a **hypothetical** ₹5.5L battery loan at 9.5% for 60 months was adopted, generating ~₹11,551/month. **This is not a verified dealership quotation.**
7. Subsequently, the EV vehicle **purchase-price assumption changed from ₹12L to ₹8L**, **excluding** the battery, while petrol remained ₹12L. The EV's earlier ₹12L assumption and earlier comparison outcomes are superseded.

## Why both cars have the same resale estimate

₹5 lakh at the end of five years is a **controlled assumption**, not a forecast of either car's used-car value. Resale is independently editable for the two cars. For an EV with a separate battery loan, the model treats ₹5L as **gross vehicle + battery sale proceeds** and deducts any battery debt outstanding at sale. Confirm saleability, lender releases and battery warranty transfer in a real deal.

## What is *not* assumed to be equal

The EV has its own charger investment, electricity consumption, battery financing and maintenance. Petrol has fuel consumption and petrol maintenance. Different cash-flow timing, especially up-front cost, can produce different opportunity costs even if some long-term amounts are the same.

## Assumption change checklist

When editing defaults:
1. Update `defaults` **and** corresponding HTML input `value` fields in `index.html`.
2. Update pre-rendered/static fallback figures in `index.html` (important for restricted iPhone file previews).
3. Verify JS calculations and the three page views; update snapshot examples here and in [calculation-method.md](calculation-method.md).
4. Update this document's decision history when a family decision supersedes a former assumption.


## Revision — 10 November 2026: third template and future real-car shortlist

The buyer plans to test-drive **actual cars** and collect actual dealer quotations. The original petrol and battery-financed EV calculations are now **permanent, editable templates**, rather than the only cars that can appear. A **self-charging petrol-electric hybrid template** has been added, while the petrol and EV defaults are unchanged.

| Additional hybrid input | Initial editable assumption | Caveat |
|---|---:|---|
| `hCost` | ₹12,00,000 on road | Assumption, not a real hybrid quotation; hybrid battery included |
| `hMileage` | 18 km/L | Illustrative combined real-world efficiency **including** electric motor assistance, not tested |
| `hGridShare` | 0% | Percent of driving km powered by **external grid energy**, 0 for self-charging |
| `hEfficiency` | 0.20 kWh/km | Used **only** for plug-in hybrid grid-electric kilometres |
| `hTariff` | ₹11/kWh | Used only if external charging is nonzero |
| `hCharger` | ₹0 upfront | Self-charging hybrid needs none; adjust for plug-in hybrid |
| `hInsurance` | ₹15,000/year | Years 2 onward, first year included in on-road purchase |
| `hMaintenance` | ₹10,000/year | Placeholder |
| `hResale` | ₹5,00,000 | Hypothetical resale after five years |

At the default 40 km/day, 5 years, 6% opportunity-cost rate, this hybrid template yields **~₹43,455/month** full equivalent economic cost. The other template values remain **₹49,619 petrol** and **₹43,301 EV**.

New actual cars will be stored separately under [`data/cars.json`](../data/cars.json), with specific quotation-derived financial overrides, status/source details, **positives, negatives and questions**. Unquoted figures are inherited from the relevant template and labelled accordingly. This enables a decision combining economic cost with test-drive experience without assigning arbitrary scores to subjective observations.

For plug-in hybrid, `hGridShare` is an approximation of kilometres powered by externally charged electricity. The remaining distance uses petrol-mode mileage and shared petrol price; charging costs use grid kWh/km, tariff and optional charger cost. For **self-charging hybrid**, electric-motor operation is already embodied in the real-world fuel economy: **do not** additionally subtract a percentage of kilometres on the theory that self-charging electric kilometres are free.

See [quote intake](adding-car-quotes.md) and [test-drive decision guidance](decision-process.md).
