# Car cost comparison

An independent, mobile-friendly car ownership comparison for a family in Kolkata, India.

**Website:** https://genus-consult.github.io/car-cost/  
**Current calculator:** [index.html](index.html)  
**Saved real-car records:** [data/cars.json](data/cars.json)  
**Documentation reviewed:** 10 November 2026

## Two related views of the purchase decision

**1. Three permanent financial templates** on the **Comparison** page:

| Template | Default on-road purchase cost | Other core assumptions | Estimated monthly economic cost at 6% |
|---|---:|---|---:|
| Petrol | ₹12,00,000 | 10 km/L, ₹114/L | ₹49,619 |
| Battery-financed electric | ₹8,00,000 **excluding battery** | Separately financed ₹5.5L battery at 9.5%/60 months, 0.20 kWh/km, ₹11/kWh, ₹75k charger | ₹43,301 |
| Petrol-electric hybrid (self-charging) | ₹12,00,000 **including battery** | 18 km/L combined real-world petrol mileage, no external charging | ₹43,455 |

All are **hypothetical illustrations, not real dealer offers**. They share 40 km/day, a five-year horizon, ₹18,000/month driver, ₹5 lakh gross resale proceeds and a 6% effective annual opportunity-cost assumption. All inputs are editable. The hybrid's 18 km/L figure is a **placeholder**, not a verified car specification.

**2. Named cars and test drives** on the new **Shortlist** page. When a dealer quotation or test-drive impression is supplied, an AI or repository editor records it in `data/cars.json`. Each car gets a modelled ownership cost under the same calculation, quotation information, and separate **positive points, negative points, and open questions**. **The three templates remain on the site** and are not overwritten.

The financial model does not automatically score subjective preferences. The eventual choice depends on **both cost and actual driving/ownership impressions**.

## What the website includes

- **Comparison:** Three generic model templates, with automatically updated monthly equivalent ownership costs.
- **Shortlist:** Named real cars from GitHub JSON, including quote inputs and subjective notes, with expandable cost breakdowns. Initially empty until the first actual quotation is provided.
- **Assumptions:** Editable vehicle, fuel, battery, charger, hybrid, driver, resale, insurance, maintenance and growth assumptions; reset to defaults.
- **Details:** Discounted cash-flow components, five-year nominal costs, recurring expenses and transparent formulas.

The site is a static, self-contained `index.html` hosted via **GitHub Pages** from `main` / `(root)`. It also fetches **`data/cars.json`** on page load to display named cars; there is no server database or login. The raw JSON and source code are in a **public repo**. Inputs changed in a visitor's browser are **not automatically written back** to GitHub.

## Project documents

- [Assumptions and decisions](docs/assumptions-and-decisions.md) — dated choices, units and superseded assumptions.
- [Calculation methodology and 6% audit](docs/calculation-method.md) — formulas, cash-flow dates, monthly equivalent, 0% comparison, independent verification.
- [Research and caveats](docs/research-and-caveats.md) — Kia BaaS, third-party and own-damage insurance, CESC, tariffs, warranty, missing evidence.
- [Adding car quotations](docs/adding-car-quotes.md) — how to store future actual vehicles, safe public data, JSON record schema.
- [Decision process / test-drive guide](docs/decision-process.md) — evidence quality, positives, negatives and conditions before purchase.
- [Maintenance and lessons](docs/maintenance-and-lessons.md) — iPhone compatibility, interface decisions, GitHub Pages, regression checks, future-AI handoff.

## When new quotes arrive

1. Share the source quotation/PDF/photos and test-drive notes in ChatGPT.
2. Extract the exact car/variant, date, Kolkata on-road amount and exclusions. Mark any estimated values clearly.
3. Add/update a record in **`data/cars.json`**, without changing the three generic templates.
4. Add dated pros/cons and open questions, keeping subjective observations separate from sourced technical facts.
5. Recheck calculations and the hosted **Shortlist** page.

**Privacy:** The repository is public. Never commit unredacted names, phone numbers, addresses, account information, customer IDs, signatures or confidential quote attachments. Keep sanitized facts, or obtain explicit permission before publishing documents.

## Important modelling caveats

- Full **equivalent monthly economic cost** includes purchase, running expenses, resale and the 6% opportunity-cost assumption; it is not literal monthly cash outflow.
- The EV battery's ~₹11,551 EMI is **hypothetical financing**, not Kia's officially guaranteed plan. Kia's advertised ₹3.30/km is an illustrative financing-equivalent headline.
- Self-charging hybrids obtain all usable energy from petrol; the electric motor's benefit is already reflected in km/L. For a plug-in hybrid, an optional grid-electric-kilometres share is available with a separate kWh/km and tariff input.
- Unquoted candidate inputs inherit generic template assumptions. A generated monthly cost is **a model result, not a dealer price**.
- The normal public URL can be sent via WhatsApp. iPhone HTML attachment previews may block JavaScript, so use the hosted link for interactivity.
