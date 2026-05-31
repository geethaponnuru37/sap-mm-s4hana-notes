# IDoc status codes — quick reference

A reference card I keep next to me. Outbound statuses are below 30, inbound 50 and above. There are more codes than this list, but these are the ones that actually turn up in tickets.

## Outbound

| Code | Meaning |
|------|---------|
| 01 | Just created, not processed yet |
| 03 | Data passed to port — dispatched at SAP layer. Check `SM58` to confirm receiver acknowledged. |
| 04 | Error in the control information — usually partner profile or distribution model |
| 05 | EDI subsystem rejected — investigate at the EDI layer |
| 12 | Dispatch confirmed by the receiver (audit IDoc) |
| 18 | Output triggered the IDoc successfully (from NAST) |
| 26 | Outbound syntax check failed — usually segment mismatch with the basic type |
| 29 | ALE service error — distribution model / partner profile inconsistency |
| 30 | Ready for dispatch, waiting. Almost always means the dispatch background job isn't running. |

## Inbound

| Code | Meaning |
|------|---------|
| 50 | IDoc has arrived, hasn't been validated yet |
| 51 | The classic application error. Read the long text. |
| 53 | Application document posted. Success. |
| 56 | IDoc added with errors — usually segment-version mismatch |
| 64 | Ready to post, waiting for the inbound job |
| 65 | ALE service error on inbound — inbound function module config |
| 66 | Waiting for a predecessor IDoc in a serialisation group |
| 68 | Marked as do-not-retry — usually a manual flag |
| 69 | Edited via `WE19` or `WE02` |
| 70 | Pre-edit copy preserved for audit |

## The 51 sub-causes I see most

When the latest status is 51, the long text is the diagnostic. Common phrasings I've come across:

- *"No vendor found for partner number X"* — vendor missing or blocked at the receiver.
- *"Plant Y not in scope"* — distribution model filter excludes that plant.
- *"Material X not maintained for sales org / dist channel"* — material extension missing.
- *"Posting period not open"* — FI period control. Hand to the FI consultant.
- *"Tax code XX does not exist"* — tax code missing in the receiver.
- *"Account determination error"* — `OBYC` / valuation class mismatch.
- *"Pricing condition record not found"* — missing condition record for the combination.

The long text usually points at the application area (FI, MM, SD, etc.), which tells me which team owns the fix.

## How I use this card

Triage. When a ticket lands, I check status first. The status code alone tells me whether it's structure, transport, or application. Then I open the long text for the specifics. Saves time vs jumping to debug mode straight away.
