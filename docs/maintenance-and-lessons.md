# Project handoff, interface decisions and development lessons

**Project:** Car cost comparison (public, independent family tool)  
**Repository:** https://github.com/genus-consult/car-cost  
**Live site:** https://genus-consult.github.io/car-cost/  
**Publishing:** GitHub Pages, branch `main`, `/(root)`, HTTPS.  
**Updated:** 7 November 2026.

## Why HTML rather than Google Sheets

A small, client-side HTML calculator is easier to share with family through WhatsApp as a normal website link. It works in ordinary iPhone/Android browsers without a Google account. All calculations can happen locally in JavaScript, so no application server or paid service is required.

Google Sheets would still be useful for audit trails and complex quote comparisons, but this project intentionally selected a **mobile-friendly web interface**. Supporting calculations appear in the **Details** and **Assumptions** sections rather than cluttering the first screen.

## Current implementation

- One self-contained `index.html` with HTML, CSS, JavaScript and **no external runtime dependencies**.
- The visible navigation has three **views**:
  - **Comparison**: the two full monthly costs, the difference, and links to other views.
  - **Assumptions**: editable driving, purchase, energy, service, insurance and battery finance numbers; reset-to-default button.
  - **Details**: present-value cost components, recurring/nominal views, formulas and caveats.
- These are **sections of one HTML application** controlled by the URL fragment (e.g. `#details`), not three separately deployed HTML files.
- Model inputs are represented in JavaScript's `defaults` object and DOM inputs; every valid edit triggers `render()`.
- No backend, online database or login. **Edits apply in the active browser session; they are not changes to the repository defaults.**
- Existing code still has internal support for reading a `?s=...` scenario URL, but the **Share scenario button was removed** at the user's request because it did not offer a sufficiently clear/reliable family-sharing experience. Publicly share the **ordinary site link** instead.

## Design requirements gathered from actual iPhone use

1. **Understated, family-oriented tone.** No hype, sales-oriented copy, oversized marketing hero or flashy language. This is not a consumer marketing website.
2. **Show the answer early:** prominent full ownership-cost figures for petrol and EV; technical methodology must not dominate the front screen.
3. **Put complexity behind views** (Assumptions / Details), but keep it fully inspectable.
4. **Units and periodicity must always be explicit.** Distinguish annual vs monthly premiums, kWh vs km, up-front cost vs EMI, nominal totals vs level monthly economic cost.
5. **No misleading dual underlines in the tab navigation.** Initially, the Comparison tab retained a CSS `.active` underline when Details was selected. This was fixed by deriving the underline solely from **`body[data-view]`** and the corresponding `data-view-target`; no stale active-class visual state. Keep exactly one selected indicator.
6. **Avoid competing 'monthly cost' labels.** 'Full ownership cost per month' includes vehicle price, resale, recurring bills and opportunity cost. 'Average recurring expenses' excludes purchase/resale/charger. Earlier less-precise labels caused understandable confusion.
7. **iPhone local file preview is not a dependable JavaScript execution environment.** A downloaded `index.html` displayed design but not calculated figures in a file preview. We added **pre-rendered default figures** as fallback and a warning when JavaScript cannot run. The recommended sharing method is a hosted HTTPS link (GitHub Pages), opened in a standard browser.
8. **GitHub Pages versus GitHub account settings:** use the **repository** Settings → Pages, not account-level 'Verified domains'. A custom domain is unnecessary. Site source is `main` + `/(root)`.
9. **Browser caching:** after updates, some iPhones may retain an older version. Refresh or visit with a harmless version parameter (e.g. `?v=2026-11-07`). This is cache busting, **not** a new financial assumption.
10. The GitHub repository is **public** for free Pages deployment; do not commit personal documents, phone numbers, unredacted quotes or confidential financial information.

## What changed during development

- Initial single-file calculator put inputs, breakdown and equations all together and used more dramatic styling. Converted to restrained, mobile-friendly Comparison/Assumptions/Details views.
- Earlier EV vehicle purchase default was **₹12L**. Changed to **₹8L excluding separately financed battery**; the petrol vehicle stayed ₹12L.
- The Share scenario action was removed; copying the public homepage URL is adequate for family.
- Corrected iPhone static fallback values and separately named **full monthly ownership cost** vs **average recurring expenses**.
- Fixed the tab underline problem via selectors tied to **which view is actually open**.
- Regression test confirmed that reducing EV electricity tariff from **₹11/kWh to ₹6.80/kWh** produces **₹42,279/month** and **₹32,622/month recurring**. This had initially looked like a conflict with the ₹43,301 default but was a **changed input**, not an error in the present-value formula.

## Safely changing the calculator later

1. Read [assumptions and decisions](assumptions-and-decisions.md), [calculation method](calculation-method.md), and [research and caveats](research-and-caveats.md).
2. Inspect the current `index.html` on `main`, especially `defaults`, `compute(a)`, `render()`, `selectPage()` and CSS selectors for `body[data-view]`.
3. If a default input changes, update **both** its JavaScript `defaults` value and HTML input `value`, and **pre-rendered fallback totals**, bars, running figures and cost-breakdown rows. Be careful not to leave stale static figures.
4. Test: fresh page defaults; change a field and watch linked outputs; 0% discount; battery loan term longer than ownership; reset; switching all three views; mobile layout; only one tab underline; no blank figures. Also test a browser with JavaScript disabled for fallback.
5. Commit changes to `main`; GitHub Pages redeploys. Confirm the public site, not only the repository file.
6. Update documents and examples in the same change as materially different assumptions or calculation rules.

## Minimal numeric regression checklist

At current defaults: petrol **₹49,619/mo**, EV **₹43,301/mo**, EV battery EMI about **₹11,551/mo**; 40 km/day, 5 years, 6% opportunity rate. At EV electricity tariff **₹6.80/kWh**, EV becomes **₹42,279/mo**. With the rate set to **0%** and other defaults unchanged, petrol is **₹45,370/mo** and EV approximately **₹39,894/mo**. Exact calculations use unrounded numbers; display rounding can move a visible row or total by ₹1.

## Future improvements (not yet implemented)

- Actual Kolkata dealer quotes for vehicle, battery financing and on-road fees; selectable quote scenarios.
- Editable, quote-driven insurance details that feed a single main input, including TP/OD timing and battery coverage.
- Detailed electricity tariffs or actual CESC bill-based marginal-cost estimation and possible separate metering.
- Battery-financing documentation and lender fees; cash down payment, balloon/foreclosure/transfer and insurance treatment.
- Clarify whether vehicle-chassis financing is desired. Current model assumes that **the vehicle itself is paid for upfront** while the EV battery is financed.
- Resale research for five-year/73,000 km vehicles, including second-owner battery warranty.
- Broader costs (tyres, parking, tolls, accidents, taxes, inflation, vehicle tax, public charging) if material.

## Suggested handoff for a future AI or developer

> This is a public, single-page GitHub Pages car-cost calculator for a first-time car buyer in Kolkata. Read `README.md` and all files in `docs/` before editing `index.html`. Preserve a simple, understated family-facing homepage. Keep monthly full economic cost distinct from recurring expenditure. EV chassis default is ₹8L excluding the separately financed battery; the ₹5.5L/9.5%/60m battery financing is hypothetical, not a verified Kia quotation. Do not double-count battery, purchase or resale. Maintain iPhone/static-preview fallback and navigation behaviour, check the numeric regression results, and update assumptions and docs whenever defaults change.
