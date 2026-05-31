# Material master views

The material master in SAP is segmented into views — Basic Data, Sales, Purchasing, MRP, Accounting, and so on. Each view holds the fields needed by a particular functional area, and the same material can exist in many plants with different view-level data per plant.

Missing or incomplete views are one of the most common defect categories I deal with. You can't post a goods receipt if accounting is missing for that plant. You can't create a PO line if purchasing isn't maintained. You can't bill if sales isn't extended. Half the "system is broken" tickets I've handled were really "material wasn't extended properly" tickets.

## The views in roughly the order they'd be maintained

**Basic Data 1 & 2** — name, base UoM, material group, division, material type (cannot be changed after first save, watch out for this), industry sector (also locked after creation), weight, volume, EAN code.

**Classification** — assignment to classes (the standard one is 001 material class), characteristic values. Drives batch determination, variant config, sometimes release strategies.

**Sales: Sales Org Data 1 & 2** — sales org, distribution channel, delivering plant default, tax classification, item category group (this one drives the order processing flow downstream).

**Sales: General/Plant Data** — availability check group, loading group, transportation group, profit centre.

**Purchasing** — purchasing group default, purchasing value key (tolerances), order unit, manufacturer part number if relevant.

**MRP 1, 2, 3, 4** — MRP type (PD, VB, ND etc), MRP controller, lot size, procurement type, safety stock, reorder point, planned delivery time. This is where planners spend most of their time.

**General Plant Data / Storage 1 & 2** — storage conditions, batch management indicator, storage bin if WM is not active.

**Warehouse Management 1 & 2** — only if WM is active. Warehouse number, storage type, picking strategy.

**Quality Management** — inspection types, QM control key.

**Accounting 1 & 2** — valuation class (this drives the GL account determination via OBYC), price control (S standard, V moving average), the price itself, price unit.

**Costing 1 & 2** — origin group, overhead group, quantity structure indicator.

## Tables I refer to most often

- `MARA` — header data, client level
- `MARC` — plant-level
- `MARD` — stock at storage location level
- `MBEW` — valuation
- `MVKE` — sales org data
- `MAKT` — descriptions by language
- `MEAN` — EAN / UPC codes

When I'm investigating "view missing" tickets, I usually go straight to MARC for the material/plant combination and check the maintained flags.

## Things that have caught me

- **MM17 vs LSMW** for mass extension — MM17 validates against material type field selection, LSMW doesn't unless you configure it carefully. I prefer MM17 unless I'm doing something unusual.
- The S/4HANA Business Partner conversion doesn't touch the material master. That changes only affected vendor and customer masters. People sometimes confuse the two.
- For materials extending to a lot of plants, **MMSC** is faster than going view-by-view in MM02.

## What I always check at UAT

If I've done a material-related change, my standard self-check before handing to the test team:
- Material is visible in all relevant plants
- Each view is maintained, not just blanked
- Account assignment posts cleanly to a test GR
- Sales order can be created (if it's a saleable material)

Those four cover 90% of post-change defects.
