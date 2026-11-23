# landlistings-data

Public data feed for Krennic's land-for-sale site (parcels of 5 acres or more in VA, PA, NC, ME, CT, VT, TN, MI, MT, RI, SC, WV).
The Grok Build app reads:

- `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/listings.json` (array of listings)
- `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/meta.json` (updated time, counts, rules, sources)

Refreshed Mon and Thu mornings (America/Chicago) by the land-listings-refresh routine. Listings come from public pages on
United Country, Mossy Oak Properties and Xome. Every listing links to its own source page; verify everything with the seller.

## Rules
- 5 acres or more; land-only asking price <= $200,000; with a house <= $600,000.
- Lots the listing or assessor says you cannot build on are left out (landlocked, no-build deed/permit/flood plain, recreational-only).
- A listing whose own page returns 404 is removed. A 403 is not removal (source throttling); it is retried next run.
- Never invented: unknown fields are null. No demographic (race, ethnicity, national origin) data, ever.

## listings.json fields
key (stable, from URL), title, state, state_name, region, county, address, lat, lon, pin_exact, acres, price, price_kind,
price_per_acre, has_house, beds, baths, sqft, auction, auction_date, source, url, value_tier (great|good|notgood|poor|none),
value_tier_label, value_reasons [{ok,text}], utilities {power, water, sewer, source_phrase}, flood {code N|M|H, zone, approx},
road {code F|E|L|U, label, source_phrase}, image_url (the listing's own public photo, or null), url_status, date_checked.
