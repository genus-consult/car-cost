# Financial calculation methodology

**Applies to:** the `compute(a)` function in the current single-file `index.html`. All amounts in INR unless marked otherwise.

## The primary output: equivalent monthly economic cost

The primary number is **an equivalent constant monthly amount** whose present value equals the present value of net ownership cash flows over the holding period.

It is not:
- a loan EMI for the whole car;
- the monthly electricity/petrol bill;
- a literal monthly bank outflow;
- upfront purchase divided by 60 without resale;
- simple depreciation plus a second full purchase charge.

The model assumes a **cash-funded vehicle purchase** at the start, plus a separate loan used **only for the EV battery**. No interest or EMI is modelled on the ₹12L petrol or ₹8L EV chassis purchase.

## Timing conventions

- At **month 0**: pay petrol purchase price; or EV purchase price plus charger/installation. Initial insurance is already included in each vehicle purchase price.
- **Months 1 to M**: driving energy cost, driver salary and pro-rata routine maintenance, assumed paid at end of each month.
- **Months 1 to loan tenure**: monthly EV battery EMI, but no EMIs after the loan has finished.
- **Months 12, 24, 36, 48** in a 60-month model: ₹15,000 renewal premiums covering Years 2, 3, 4, 5, respectively. Insurance is not spread as an actual cash payment each month.
- **Month M = 60**: collect gross resale proceeds, and settle any outstanding battery-loan principal (if loan term exceeds holding period).

The model uses **365 days/year**, an annual driving distance of daily km × 365, and a model-month mean of annual kilometres ÷ 12.

## Step 1 — Convert opportunity rate to monthly

The default 6% is an **effective annual** opportunity-cost assumption:

```text
r_month = (1 + annual_rate/100)^(1/12) - 1
        ≈ 0.4868% per month when annual_rate = 6
M = years * 12
```

This is a *discount rate*, not a car-loan or battery-loan interest charge. It represents an assumed alternative return on capital and is not a guaranteed tax-free investment yield.

## Step 2 — Battery EMI and balance

The battery financing illustration uses a **nominal annual lending rate** with monthly accrual, not the effective-monthly formula above.

```text
i = loanAPR / 1200
EMI = loanPrincipal * i / (1 - (1 + i)^(-loanMonths))
if i = 0, EMI = loanPrincipal / loanMonths

After k payments where k < loanMonths:
loanBalance(k) = P*(1+i)^k - EMI * [((1+i)^k - 1)/i]
if i = 0: loanBalance(k) = max(0, P - k*EMI)
if k >= loanMonths: loanBalance = 0
```

At defaults, P = ₹5,50,000, APR = 9.5%, term = 60 months, EMI ≈ ₹11,551/month and month-60 remaining principal = ₹0. If the car is sold before the loan finishes, the model adds its outstanding principal as a month-M cash expense; it assumes **no foreclosure charges or loan transfer**, both of which require contract-specific treatment.

The battery is **not** separately charged again as an upfront ₹5.5L capital purchase; doing so would double-count the same amount.

## Step 3 — Price growth, consumption and nominal expenses

For model month `m`, `year_index = floor((m-1)/12)`.

```text
monthly_km = dailyKm * 365 / 12
petrol_cost_m = monthly_km / pMileage * pFuel * (1+gFuel/100)^year_index
EV_power_cost_m = monthly_km * eEfficiency * eTariff * (1+gElec/100)^year_index
driver_m = driver * (1+gDriver/100)^year_index
petrol_service_m = pMaintenance / 12 * (1+gService/100)^year_index
EV_service_m = eMaintenance / 12 * (1+gService/100)^year_index
```

Insurance renewal for Year 2 uses the entered base value. Subsequent renewal premiums rise by `(1+gInsurance/100)^(year-2)`. All escalation defaults are **0%**.

## Step 4 — Discount cash flows and levelise

For a cash flow `CF_t` in month `t`, its present value is:

```text
PV(CF_t) = CF_t / (1+r_month)^t

PV_total = upfront purchase + (EV charger if applicable)
         + PV(all recurring cash outflows)
         + PV(insurance renewal payments)
         + PV(battery loan settlement if owed)
         - PV(gross resale proceeds)

monthly_annuity_factor = sum[t=1..M] 1/(1+r_month)^t
equivalent_monthly_cost = PV_total / monthly_annuity_factor
```

If the opportunity-cost rate is **0%**: `PV(CF_t) = CF_t` and the annuity factor becomes M, so **monthly cost = (nominal expenses − nominal resale) / M**. This *removes time-value/opportunity-cost adjustments*, but **does not remove 9.5% battery loan interest**, because battery EMI is still based on the loan contract assumption.

A cost-component's monthly equivalent on the Details tab = `PV(component) / monthly_annuity_factor`. Summed components match the total using full precision; rounding each visible row can create tiny differences.

## Current default checks (7 Nov 2026)

| Metric | Petrol | EV with ₹11/kWh |
|---|---:|---:|
| Full equivalent monthly economic cost (6%) | **₹49,619** | **₹43,301** |
| Avg monthly recurring expenses (nominal) | ₹33,703 | ₹33,644 |
| Total net nominal cost over five years | ₹27.22 lakh | ~₹23.94 lakh |
| Full equivalent monthly cost (0% opportunity rate) | ₹45,370 | ~₹39,894 |

These are **rounded displayed outputs**, not independent dealer or lender quotes.

A useful regression check: with **only** `eTariff` changed from ₹11 to **₹6.80/kWh**, the EV's 6% full cost becomes **~₹42,279/month**, recurring expenses become **~₹32,622/month**, and 5-year nominal net becomes **~₹23.32 lakh**. That exact input change explained a previously observed screenshot discrepancy. Another regression check: raising **EV chassis price alone** from ₹8L to ₹9L yields **~₹45,227/month** at 6% and ₹11/kWh.

## Interpret carefully

- Equal driver salary, renewal insurance and resale values can cancel in the **difference** between the two choices, while still increasing both full-cost totals.
- A ₹5L resale amount **subtracts a cash inflow** at month 60; it is not another expense. Do not include both full upfront cost and depreciation as separate charges.
- The nominal average of recurring expenses is **not** the full economic cost because it excludes upfront car price, charger, resale and any end-of-horizon battery loan settlement.
- A 6% discount rate is a *choice*; 0% is also valid if the user wants a simple nominal expenditure comparison.
- The default EV battery loan has a higher 9.5% lending rate: that is an actual assumed borrowing cost, distinct from the 6% opportunity rate.
- This model is intentionally deterministic. Sensitivity to distance, consumption, battery loan terms, tariff, resale and vehicle price should be explored before using it for a purchase decision.


## Independent opportunity-cost audit — 10 November 2026

**Result:** reviewed the current `index.html` `compute(a)` calculation, then independently recalculated each default cash-flow category. **No incorrect application or omission of the annual 6% rate was found in the *full equivalent monthly economic cost*.** The secondary `nominal net cost` and `average recurring expenses` are intentionally **not** discounted.

Every cash flow enters through `add(obj, name, amount, month)`, which saves **both** (a) its unadjusted nominal amount and (b) its present value using `amount/(1+monthly_rate)^month`. The per-category PV values are summed and divided by the **same 60-month annuity factor** to obtain full economic monthly cost. The sole t=0 purchases (and EV charger) have PV equal to their nominal value, but their conversion to a monthly equivalent **does** reflect opportunity cost.

The audit independently recreated the following component effects using current defaults, before per-row rounding:

| Component | Equivalent ₹/month at 0% | Equivalent ₹/month at 6% | Why |
|---|---:|---:|---|
| Petrol vehicle purchase (₹12L at t=0) | 20,000.00 | 23,110.78 | Upfront capital incurs opportunity cost |
| EV vehicle purchase (₹8L at t=0) | 13,333.33 | 15,407.19 | Same principle |
| Resale proceeds (₹5L in month 60, both) | −8,333.33 | −7,195.72 | Future receipts have a lower present value |
| Driver salary (₹18k each month, both) | 18,000.00 | 18,000.00 | Discounting and level-payment conversion **exactly cancel** |
| Petrol (₹13,870 each month) | 13,870.00 | 13,870.00 | Same cancellation |
| EV electricity (₹2,676.67 each month) | 2,676.67 | 2,676.67 | Same cancellation |
| Petrol maintenance (₹833.33/month) | 833.33 | 833.33 | Same cancellation |
| Insurance (₹15k in months 12,24,36,48) | 1,000.00 | 1,001.02 | Periodic renewal timing differs from an equal monthly stream |
| EV charger (₹75k at t=0) | 1,250.00 | 1,444.42 | Upfront investment |
| Battery EMI (₹11,551.02 monthly, all 60 months) | 11,551.02 | 11,551.02 | Same cancellation; **the EMI itself** separately reflects 9.5% loan interest |

The independently calculated **full totals** were:

- **Petrol:** 0% = ₹45,370.00/mo; 6% = ₹49,619.41/mo.
- **EV:** 0% = ₹39,894.36/mo; 6% = ₹43,301.27/mo.

The fact that regular identical monthly costs display the *same amount* at 6% **does not mean they were excluded**: their PVs decrease, but the denominator used to turn PV into level monthly cost decreases proportionately.

### Checks beyond the default scenario

1. **Non-flat running cost:** increasing petrol prices by 5% after each year makes the petrol expense's equivalent monthly cost **₹15,328.10 at 0%** and **₹15,241.25 at 6%**, demonstrating that the opportunity rate affects running costs when their amounts change over time.
2. **Battery debt beyond car ownership:** with a hypothetical **96-month ₹5.5L loan at 9.5%**, 60 months of battery EMIs and the **~₹2.56L remaining balance paid at month 60** are both included; the settlement is discounted at the opportunity rate just like resale.
3. **0% rate:** every PV factor correctly becomes 1 and the annuity factor becomes 60, while the battery's **9.5% loan rate remains unchanged**.

### Modelling limitations, not discounting defects

- Maintenance is assumed paid as equal month-end instalments, even if real servicing is periodic.
- Recurring energy and driver costs are assumed at month-end. Annual insurance is prepaid at months 12/24/36/48, representing the starts of Years 2–5.
- Annual energy-price and salary escalation defaults are **0%**; the 6% rate is treated as a nominal/effective annual alternative return, not a guarantee or an after-tax yield. Tax on investment returns, inflation uncertainty and lender fees are not explicitly modelled.
- The battery-financing schedule uses **loan APR** to set contractual EMIs and the separate **opportunity rate** for valuation, which is intentional.
- No changes to the calculator formulas were warranted from this audit.


## 10 November 2026 extension — hybrid model and quoted-car reuse

A **third `h` cash-flow object** is now constructed inside the same `compute(a)` function as the existing petrol `p` and EV `e`. Each hybrid cash flow uses the **same discount function**, annuity denominator and ownership horizon. The hybrid includes purchase at month 0, possible charger at month 0, driver/energy/maintenance each month, Years 2+ annual insurance at renewal timing, and gross resale at month M. The standard hybrid template assumes **no separate battery loan**, because its battery is included in vehicle price.

```text
grid_share = hGridShare / 100    // 0 ... 1
monthly_distance = dailyKm * 365 / 12
hybrid_monthly_energy_cost =
  monthly_distance * (1-grid_share) / hMileage * petrol_price * fuel_escalation
  + monthly_distance * grid_share * hEfficiency * hTariff * electricity_escalation

hybrid_full_monthly_cost = PV(all hybrid cash flows) / same_monthly_annuity_factor
```

**Interpretation:** `grid_share=0` represents a *self-charging* petrol-electric hybrid. Its real-world `hMileage` must already incorporate electric motor assistance. `grid_share>0` is an **approximate plug-in hybrid** case: only grid-charged distance pays electricity bills, and remaining distance uses petrol-mode mileage. A plug-in hybrid with poor charge discipline can be represented by reducing grid share. Do not confuse motor-electric driving in a self-charging hybrid with electricity bought from CESC.

**New default regression:** hybrid full economic cost **~₹43,455/month** at 6%, **~₹39,140/month** at 0%, with 18 km/L and no grid charging. (See checked model output for the precise 0% value before treating this approximation as a formal snapshot.) The existing petrol and EV checks remain intact.

**Named cars:** shortlist records are stored in `data/cars.json`. The app overlays allowed, vehicle-specific `overrides` onto the current editable shared defaults and calls the exact same `compute(a)` function. Powertrain selects the appropriate branch: petrol→`p`; battery-financed EV / outright EV→`e`; hybrid→`h`. Outright EV forces battery loan principal to zero, since that vehicle price includes the battery. A missing vehicle-specific input is **not treated as a quotation**: it inherits the generic template input. The app explicitly displays this inherited-estimate caveat.

The discount mechanism itself has **not been changed** by the addition of hybrid and named cars.
