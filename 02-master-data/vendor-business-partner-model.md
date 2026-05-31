# Vendor master and the Business Partner model

In ECC, vendors lived in `LFA1` and customers in `KNA1`. In S/4HANA, both got pulled into a single concept — the Business Partner. A BP has one or more roles attached to it. For a supplier you'd typically see role `FLVN00` (Supplier) and `FLVN01` (Supplier — Financial Accounting), and the underlying LFA1 / LFB1 tables are kept in sync through a layer called CVI (Customer-Vendor Integration).

This is one of the biggest day-one differences for anyone coming from ECC.

## The headline change

- `XK01` / `XK02` / `XK03` (and `MK01` / `FK01` and the customer equivalents) — all still work, but they now redirect to `BP` under the hood. You can also call `BP` directly.
- A BP has shared general data (name, address, communication) plus role-specific sections.
- For a vendor, you need at least one supplier role and usually the company-code level Supplier (Financial Accounting) role too.

The Customer-Vendor Integration layer keeps `LFA1` / `LFB1` / `KNA1` / `KNB1` populated. The legacy tables are still there for compatibility with custom reports and ABAP code.

## Account groups vs BP grouping

The ECC concept of "account group" mapped onto a few things at once: field selection, number range, partner schema, and tax category. In S/4HANA those are split:

- **BP grouping** — drives number range and (with the role) drives field selection
- **BP role** — drives the data sections you see for that BP
- **BP relationships** — replaces some of the older partner-determination logic

When migrating from ECC, the account-group-to-BP-grouping mapping is one of the critical CVI customising steps. I've seen migrations where this wasn't planned carefully and the team spent weeks cleaning up afterwards.

## Tables to know

The legacy tables are still in use:

- `LFA1` — vendor general (legacy)
- `LFB1` — vendor company code (legacy)
- `LFM1` — vendor purchasing org (legacy)
- `BUT000` — BP header
- `BUT020` — BP addresses
- `BUT100` — BP role assignment

A mismatch between BUT000 and LFA1 is the strongest signal that CVI sync hasn't worked, usually after a migration cutover or after a manual data fix that bypassed the BP transaction.

## Where I've seen problems

- **BP exists but vendor isn't usable** — CVI didn't post the LFA1 entry. Fix: rerun the CVI sync (`MDS_LOAD_COCKPIT` or the relevant report depending on the system).
- **Tax fields missing** — these live inside the FLVN01 role under the company-code section, not in the general role. Easy to overlook when setting up a new vendor.
- **Partner functions not defaulting** — config is via BP role and BP grouping in the new model. Looking on the old account group screen won't help.
- **Withholding tax flags not appearing** — also inside FLVN01. Same trap as the regular tax fields.

## My practical advice

For new implementations: design the BP grouping structure deliberately. Don't create a grouping per vendor sub-type — group them sensibly by behaviour, not by category. Changing it later is painful.

The "Time-Dependency" toggle for BP roles is powerful but easy to misuse. It lets role data change over time (so you can see what a vendor's bank details were two years ago). Only enable it if business actually needs that history. Otherwise it just complicates queries.

For migrations specifically: invest time in partner function mapping and tax data validation. Those are the two areas where data quality issues bite hardest after go-live.
