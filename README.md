# BALAR SPARES — Inventory & Sales Intelligence

A single-page inventory tool for an Amazon India spare-parts catalogue. It reads a sales and
stock history and works out what to reorder before it runs out, what is selling faster or
slower than last month, and which stock has stopped moving.

**Live:** https://fbatman1505-ha.github.io/balar-spares/

## What it does

| Tab | Question it answers |
|---|---|
| Overview | How is the business doing, and where is the money stuck? |
| Reorder | What is running short, what it costs, whether the quote is worth accepting, and what goes to the supplier |
| Pricing | The lowest price each part can be sold at, and whether a rival's price can be matched |
| What's Selling | What is moving, accelerating, or fading? |
| Update Data | Add today's figures, by form or by CSV. |
| Ask | Any of the above in plain English, plus dead stock, stockouts and overstock, which have no tab of their own |

## Reorder

One tab answers both halves: what is running short, and whether to buy it at the quoted price.

For every short line it compares the supplier's current quote against every price that supplier
has charged for that part before — last paid, median, best ever — and then works out what one
unit actually earns after Amazon's referral fee, closing fee, shipping and the GST charged on
those fees. Three outcomes follow:

- **Buy** — the margin clears the target as things stand.
- **Reprice** — it clears the target only at a higher selling price, and that price is shown.
- **Hold** — no price rise inside the owner's ceiling rescues it, so the drafted message
  negotiates on price instead of placing an order.

Each line is routed to automatic sending or to manual approval by order value. The target
margin, the price ceiling and the auto-send limit are inputs on the page, not constants in the
code: change one and every line re-decides.

**The message is real; the send is yours.** The page builds a `wa.me` link and opens WhatsApp
with the message already written, against the supplier number you set under *Who you order from*.
You press send. Numbers are stored in your own browser, never in this file and never on a server,
and the six shipped with the demo are invented and blocked from sending until you replace them.

It stops short of sending by itself on purpose. Automatic sending needs the WhatsApp Business
Cloud API, which requires a phone number *other than your own* — a number registered to that API
can no longer be used with the regular WhatsApp app — plus Meta business verification, templates
approved in advance, and a server to hold the access token, which a single public HTML file
cannot safely do. Libraries that drive the ordinary app are against WhatsApp's terms of service.

A repriced line is also only half done when the order goes out, because the Amazon listing still
has to be changed by hand, so the outbox carries that as work outstanding.

## Where the quotes come from

Three ways in, under *Update a quote*: type one in, paste the supplier's actual reply and have the
price pulled out of it by pattern match, or import a price list as CSV
(`sku, unit_price_inr`, optionally `supplier_id` and `quoted_on`). Anything entered overrides the
file and is stored in your browser; the supplied figures are never edited and can be restored.

A quote carries its date and is aged against the last day the dataset covers — the same reference
every other figure here uses. Past 14 days it is flagged in the table rather than quietly counted,
because a tool that decides on month-old prices is giving a confident wrong answer.

The quotes shipped in `balar_quotes.csv` are generated, like the rest of the dataset: each one is
the last invented purchase price multiplied by a random factor. Six parts had that factor set
deliberately so the demonstration exercises all three decision branches. Nothing here was
discovered in the data; the data was built to exercise the logic.

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

**Leaving closing stock blank does not mean "no change".** It means the figure is derived:
yesterday's stock less whatever sold. So a row with 0 sold and a blank stock box changes
nothing. If you have received new stock, type the new shelf count into the stock box.
Re-entering a date that already exists replaces that day rather than adding to it.
`balar_daily_update_template.csv` in this repo is a starting point.

## Data

The dataset is synthetic: 80 parts over 120 days, modelled on a real catalogue's structure,
price bands and supplier lead times. No real customer, supplier or marketplace data is
included.

The procurement layer is synthetic too: `balar_purchase_history.csv` (433 past purchases) and
`balar_quotes.csv` (current quotes) are generated. The six suppliers, their names and their
phone numbers are invented — `+91 90000 0000x` is not a real number range. The Amazon fee
figures are stated assumptions for the demonstration, editable on the page, and should be
replaced with a real Seller Central schedule before any margin here is trusted.
