# Report format

One plain-text email. About 12 to 15 lines. No markdown symbols.

Subject: `Nightly routine — DD-MM-YYYY`

## Structure

1. META ADS: one line per product, saying what happened and why.
2. NEW BATCH ALERT: one line.
3. SHOPIFY: one line.
4. A closing line.

## Example (invented data)

```
Nightly routine — 15-03-2026

META ADS
Product A: paused one ad, spent past the kill threshold with no sales.
Product B: running, CPA under break-even, no change.
Product C: no spend today, campaign paused since last week.

NEW BATCH ALERT
Product B has a new batch of ads in review, first look tomorrow.

SHOPIFY
4 orders today, all consistent with Meta purchases.

All good. Nothing needs you tonight.
```

The long detail (exact CPAs, IDs) stays in the run output for debugging and is not emailed.
