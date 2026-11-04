# BALAR SPARES — Inventory & Sales Intelligence

A single-page inventory tool for an Amazon India spare-parts catalogue. It reads a sales and
stock history and works out what to reorder before it runs out, what is selling faster or
slower than last month, and which stock has stopped moving.

**Live:** https://fbatman1505-ha.github.io/balar-spares/

## What it does

| Tab | Question it answers |
|---|---|
| Overview | How is the business doing, and where is the money stuck? |
| Reorder | What do I buy this week, in what quantity, at what cost? |
| What's Selling | What is moving, accelerating, or fading? |
| Dead Stock | What has stopped selling, and how much capital is in it? |
| Update Data | Add today's figures, by form or by CSV. |
| Ask | Any of the above, in plain English. |

## How it works

Every decision-driving figure — velocity, days of cover, reorder point, order quantity,
urgency tier, dead-stock status, trend, capital tied — is computed by a fixed rule, not
estimated. The rules are documented in the report accompanying this project.

The whole thing is one self-contained HTML file with no dependencies, no build step and no
network calls. It runs offline from a local copy. Rows you add are stored in your own browser
and can be exported to CSV and re-imported elsewhere.

## Updating it day by day

Use the **Update Data** tab. Either type one row at a time, or upload a CSV with the columns:

```
date,sku,units_sold,closing_stock
2026-11-05,BLR-1003,12,40
2026-11-05,BLR-1011,7,
```

Closing stock may be left blank — it is derived from the previous day's balance less what
sold. Re-entering a date that already exists replaces that day rather than adding to it.
`balar_daily_update_template.csv` in this repo is a starting point.

## Data

The dataset is synthetic: 80 parts over 120 days, modelled on a real catalogue's structure,
price bands and supplier lead times. No real customer, supplier or marketplace data is
included.
