# Adding real cars, quotations and test-drive notes

The calculator keeps the **three generic model templates** (petrol, battery-financed EV, hybrid) permanently. Named cars are saved separately in [`data/cars.json`](../data/cars.json) and appear in the **Shortlist** view when entered. The shortlist can grow without deleting or replacing the template examples.

## Safe intake workflow for future updates

When the buyer sends a dealer quotation, photo or PDF, or describes a test drive:

1. Extract the **car, variant, propulsion type**, date, quoted **Kolkata on-road** price and whether insurance, registration, charger, battery and handling costs are included.
2. **Verify arithmetic** in the quotation, including financing principal, tenure, APR, processing fees, down payment, EMI and total payable where possible. Keep any uncertainty as a pending question.
3. Ask only if a critical ambiguity prevents the model from remaining internally consistent; otherwise save what is known and label missing details **assumed**.
4. Use a stable unique `id` (e.g. `hyundai-exter-sx-2026`) in `data/cars.json`. Never overwrite a different car by accident. Add to `cars` (or update the exact matching record) with `overrides` for verified/modelled inputs, and date/source details.
5. Record **positives**, **negatives**, and **questions** as distinct, attributed notes. Notes must be specific and decision-relevant, e.g. visibility, rear-seat room, suspension, parking, CNG/turbo power delivery, service access or warranty—not generic promotional claims.
6. If there is a real document, keep the **source reference** and derived figures. **The repository is public**: do not commit quotations containing the buyer's phone number, address, customer ID, bank details or other private identifiers. Prefer a redacted PDF if the buyer explicitly wants one public, otherwise store a factual sanitized transcription in a document.
7. Commit and check the hosted site. Don't accidentally update the template defaults when entering an actual car.

## JSON schema conventions

The site expects a root JSON object:

```json
{
  "schemaVersion": 1,
  "updatedAt": "2026-11-10",
  "cars": [
    {
      "id": "example-car-variant-2026",
      "name": "Example car",
      "variant": "Example variant",
      "powertrain": "petrol",
      "status": "Quoted",
      "quote": {
        "date": "2026-11-10",
        "dealer": "Dealer or dealership",
        "city": "Kolkata",
        "reference": "Quotation # or sanitized text reference",
        "inclusions": "On-road, Year-1 insurance included",
        "notes": "Any excluded charger, fees, or battery"
      },
      "overrides": {
        "pCost": 1190000,
        "pMileage": 11.5
      },
      "pros": ["Grounded positive observation; note who observed it"],
      "cons": ["Grounded negative observation; note who observed it"],
      "questions": ["Unresolved purchase question"],
      "notes": "Short context summary"
    }
  ]
}
```

Use `powertrain` **`petrol`**, **`ev-baas`**, **`ev-outright`** (battery included in vehicle price), or **`hybrid`**. Use `status` such as **Research**, **Quoted**, **Test driven**, **Shortlisted**, or **Rejected**; they are descriptive and do not affect the economic formula.

**Important:** The JSON example above is a *schema illustration only*, not a real quote or the current car list.

### Allowed car-level overrides

Use *only* relevant input keys, with their same meanings and units as `index.html`:

- **petrol:** `pCost`, `pMileage`, `pInsurance`, `pMaintenance`, `pResale`.
- **ev-baas:** `eCost`, `eEfficiency`, `eTariff`, `eCharger`, `eInsurance`, `eMaintenance`, `eResale`, `loanPrincipal`, `loanAPR`, `loanMonths`.
- **ev-outright:** `eCost`, `eEfficiency`, `eTariff`, `eCharger`, `eInsurance`, `eMaintenance`, `eResale`. The battery is included in `eCost` and the separate battery-loan balance is forced to zero.
- **hybrid:** `hCost`, `hMileage`, `hGridShare`, `hEfficiency`, `hTariff`, `hCharger`, `hInsurance`, `hMaintenance`, `hResale`.

The family-wide assumptions (e.g. `dailyKm`, `driver`, `discount`, `years`, `pFuel`) remain shared with the current live model to make comparisons fair. Enter car-specific electricity prices only if that truly applies to the car, not simply because a utility tariff changed.

The app uses **template fallback values for every omitted override**. A result is therefore a **modelled cost**, *not* proof that every input was quoted. Every displayed car shows which fields were overridden by sourced/recorded data and how many continue to use generic assumptions.

### Hybrid definition

The hybrid template defaults to a **self-charging petrol-electric strong hybrid** with **no external electricity** (`hGridShare=0`). Its `hMileage` is the **overall real-world km/L** including motor assistance and regenerative braking; do not count the battery-assisted kilometres again as free or grid-charged kilometres. For **plug-in hybrids**, `hGridShare` is the estimated **fraction of actual driving kilometres powered by electricity from the grid**, and remaining kilometres use petrol-mode `hMileage`. Then `hEfficiency` is wall kWh/km for the grid-charged part, with `hTariff` and `hCharger` as appropriate. This is an approximation requiring real test-drive and usage assumptions.

## Important limitations

- No car has been added yet; the initial `cars` list is empty.
- Dealers' quoted 'on road' figures may have different insurance and tax inclusions. Normalize carefully before comparing.
- The battery-financed EV may need an additional loan balance/foreclosure settlement at resale. Keep that in finance inputs and notes.
- Resale values, fuel efficiency, battery warranty and maintenance rarely come from a new-car quotation: mark as scenario assumptions until backed by evidence.
- The subjective notes inform the **human purchase decision**, not a hidden numerical score. Do not silently assign weights to comfort or other subjective factors.
