# Car cost comparison

A small, independent **family car ownership-cost calculator** comparing a petrol car with a battery-financed electric car over a five-year period. Built for a first-time buyer in Kolkata, India. It is **not affiliated with Kia, CESC, any insurer, or a financing provider**.

**Website:** https://genus-consult.github.io/car-cost/  
**Source:** [index.html](index.html)  
**Last documented:** 7 November 2026

## Read this first

The site estimates **equivalent monthly economic cost** (purchase, energy, driver, annual maintenance/insurance, resale, charger, EV battery loan and a chosen opportunity-cost rate). This is **not the same** as monthly out-of-pocket spending.

Default comparison, under the agreed **6% annual opportunity-cost** assumption:

| | Petrol | Battery-financed EV |
|---|---:|---:|
| Assumed initial vehicle price | ₹12 lakh on road | ₹8 lakh on road, **battery excluded** |
| Estimated full economic monthly cost | **₹49,619** | **₹43,301** |

The EV has an **additional assumed ₹5.5 lakh battery loan** at **9.5% for 60 months** (EMI ~₹11,551/month), plus a ₹75,000 home charger. Both cars use 40 km/day and an assumed ₹5 lakh gross resale value after five years. Prices are **illustrations** and all inputs are editable. **Neither the vehicle prices nor the battery financing represent verified purchase quotations.**

## Project knowledge base

These files are intended to preserve the entire decision and reasoning trail if the original conversation is unavailable:

1. **[Assumptions and decisions](docs/assumptions-and-decisions.md)** — full input table, units, choices, revisions, derived usage, and what superseded the old ₹12L EV assumption.
2. **[Calculation methodology](docs/calculation-method.md)** — precise formulas, cash-flow timing, 6% vs 0%, battery EMI, resale accounting, worked figures and regression checks.
3. **[Research, evidence and caveats](docs/research-and-caveats.md)** — Kia Syros EV BaaS findings, source links, insurance basics, CESC charging assumptions, unresolved questions for dealers, and uncertainty labels.
4. **[Development handoff and lessons](docs/maintenance-and-lessons.md)** — GitHub Pages setup, iPhone preview limitations, simple family-facing design decisions, corrected navigation, sharing decisions, QA instructions and future work.

**Important research distinction:** Kia publicly advertises BaaS starting at ₹7.99L *ex-showroom* with an illustrative battery EMI of ₹3.30/km based on 2,200 km/month. The model's **₹8L on-road vehicle assumption and ₹11,551/month battery EMI are not Kia quotations**. Do not multiply the marketed ₹3.30 by the buyer's actual kilometres and treat that as a contractual billing rate without written confirmation.

## How to use and share

Open the [public site](https://genus-consult.github.io/car-cost/) on iPhone, Android or a desktop browser. Use the **Comparison**, **Assumptions** and **Details** tabs. Edit inputs to recalculate; use **Reset** to return to the agreed defaults. Family members may freely use the same website URL.

The **Share scenario** button was intentionally removed; sharing the homepage URL sends the current **default model**, not whatever one person edited in their browser. If a number differs between devices, compare the assumptions on each device.

The site is hosted by **GitHub Pages** from branch `main`, folder `/(root)`. It is a single self-contained `index.html`, with no required server, login or subscription. Some iPhone **file-preview** apps block JavaScript when opening an HTML attachment; use the hosted HTTPS site for interactive calculations.

## Updating the model

Read the linked documents before making changes. Edit `index.html`, including **JavaScript defaults, matching HTML input default values and pre-rendered fallback figures**, then test calculations, responsive iPhone display and navigation. GitHub Pages publishes changes committed to `main`.

The repository is **public**; do not upload private correspondence, identifying details, confidential quotations or account information without proper permission and redaction.

## Next decisions

The biggest unresolved financial inputs are **actual Kolkata on-road prices**, a **written battery-loan schedule and sale/foreclosure rights**, **five-year resale estimates**, insurance quotations and an **actual marginal CESC charging cost**. See the [research checklist](docs/research-and-caveats.md#questions-to-get-answered-in-writing-by-dealer--financing-partner).
