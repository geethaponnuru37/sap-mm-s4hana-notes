# Purchase document types — the ones I actually use

Document type controls a lot more than people initially realise — field selection, release strategy, number ranges, allowed item categories, output messaging. So even small changes to document type configuration tend to ripple.

Below are the types I see in most engagements and what each is normally used for.

## Purchase requisitions

- `NB` — standard, default for most manual PRs
- `RV` — outline-agreement requisition (when PR is tied to a contract)
- `KB` — subcontracting
- `UB` — stock transport

## Purchase orders

- `NB` — standard external vendor PO. The workhorse.
- `UB` — stock transport order, same company code
- `NBS` — stock transport order, *different* company codes (so intercompany)
- `FO` — framework order, often for service procurement with a value cap
- `RV` — order against an outline agreement

## Outline agreements

- `MK` — value contract (total value cap)
- `WK` — quantity contract (total quantity cap)
- `LP` — scheduling agreement (delivery schedules against the agreement)

## Item categories worth knowing

Independent of document type, the item category controls the behaviour at line level.

- *blank* — standard
- `L` — subcontracting
- `K` — consignment
- `D` — service
- `B` — limit (for unplanned services with value cap)
- `T` — text item (no procurement, just info)
- `U` — stock transport

## What's actually customised per document type

Each doc type carries its own setup under SPRO → Materials Management → Purchasing → [Doc category] → Define Document Types. The main settings:

- Number range (internal or external)
- Field selection key
- Screen layout key
- Output determination procedure
- Release strategy assignment
- Allowed item categories
- Allowed account assignment categories

When you create a new document type, you have to think through all of these — most teams forget at least one and find out at UAT.

## A trap I've fallen into

When a business says "we need a new document type for X", the right reflex is to ask whether field selection on an existing type, plus maybe an extra release strategy, can do the job. New document type means new number range, new release strategy entries, new output determination, often new EDI mapping on the vendor side. Easy to underestimate the work.

I've made the mistake of creating new types when the business actually just wanted a different approval path. Now I always push back at least once before adding a new document type.

## Screen field selection — the gotcha

Field selection runs at two levels: doc type (in customising) and item category (also in customising). The system applies the **most restrictive** of the two for each field. So a field you marked as mandatory at doc-type level can become hidden if the item category hides it. Always check both before debugging "why is this field not showing".
