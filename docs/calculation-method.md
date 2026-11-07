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
