# landlistings-data

Public data feeds for Krennic's site: Land (parcels of 5 acres or more in VA, PA, NC, ME, CT, VT, TN, MI, MT, RI, SC, WV),
Rentals (Windsong Ranch / Prosper TX) and SUVs (used luxury SUVs near Dallas). The Grok Build app reads these 5 files:

| File | Raw URL | What |
|---|---|---|
| data/listings.json | `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/listings.json` | Land: array of listings |
| data/meta.json | `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/meta.json` | Land: updated time, counts, rules, sources |
| data/rentals.json | `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/rentals.json` | Rentals: array of homes |
| data/suvs.json | `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/suvs.json` | SUVs: array of cars |
| data/market-meta.json | `https://raw.githubusercontent.com/krennic212/landlistings-data/main/data/market-meta.json` | Rentals + SUVs: updatedAt, updated_label, counts, rules, sources, scan times |

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

## Rentals rules (rentals.json)
- Lowest asking rent $1,800-$2,300; 2+ beds; verified drive of 20 min or less to Mrs. Jerry Bryant Elementary (never estimated).
- In-unit washer/dryer + fridge: homes whose listing says no (hookups only, no fridge) are removed. Homes whose listing does not say
  are kept and marked (`appliances_confirmed: false`, `appliances_status`). Dark kitchen cabinets removed.

rentals.json fields: key, address, city, state, zip, rent, rent_max, beds, baths, sqft, sqft_max, price_per_sqft, type, community,
miles_to_windsong, school_minutes, school_miles, school, available, pets, move_in_special, appliances_confirmed,
appliances_status (confirmed | washer/dryer confirmed, fridge not listed | fridge confirmed, washer/dryer not listed | not listed),
appliance_note, kitchen_cabinets, days_on_market, is_new, price_drop, prev_rent, first_seen, lat, lon, url, links {site: url},
sources [site], image_url, photos [url], value_tier (Great value | Good value | Not good value | Poor value), value_reasons [{mark, text}].

## SUV rules (suvs.json)
- Used luxury SUVs 2022 or newer, under 70,000 miles, asking $70,000 or less, within 200 miles of Dallas.
- No Cadillac, Range Rover Evoque or Velar. XC40s white exterior only.
- Apple CarPlay: cars confirmed without it are removed; cars whose listing does not say are kept and marked "CarPlay not confirmed".
- KBB numbers only where KBB lists a Fair Purchase Price; otherwise null / "Not listed".

suvs.json fields: key, vin, year, make, model, trim, title, family, size_class, price, miles, exterior_color, dealer, city,
miles_from_dallas, est_payment, kbb_value, kbb_label, kbb_low, kbb_high, kbb_discount (KBB minus asking; positive = below KBB),
kbb_url, site_deal_label, site_below_market, carplay_status (Has Apple CarPlay | CarPlay not confirmed), massage_seats,
new_this_week, prev_price, price_drop, first_seen, url, links {site: url}, sources [site], image_url, photos [url].

Unknown text is "Not listed", unknown numbers are null, photos are only the listings' own photo URLs ([] when none).
Rentals/SUV files are written by scripts/export_market_feeds.py on the data box, Mon and Thu mornings (America/Chicago).
