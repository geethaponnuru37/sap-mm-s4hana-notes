# IDoc troubleshooting — how I actually work through a failure

This is the rough sequence I follow when an IDoc ticket lands. It's evolved over my AMS years — I'd say 70% of failures are resolved by step 2, but the discipline of going through all the steps is what catches the tricky ones.

## Step 0 — get the IDoc number

Tickets often come with the number embedded. If not, I ask:
- Time window
- Sender / receiver system
- Message type
- A business reference — PO number, vendor number, article number, whatever

With that I can find the IDoc in `WE02` or `WE05`.

## Step 1 — read the control record

Open in `WE02`. The header (`EDIDC`) tells me:
- Direction (outbound 1 / inbound 2)
- Current status
- Sender and receiver — are they correct?
- Message type / basic type / extension

These five facts shape the rest of the investigation.

## Step 2 — read the latest status text

Status records (`EDIDS`) carry every step the IDoc has been through. Scroll to the most recent. The status text is usually descriptive enough to point me at the cause.

Quick reading guide:

- Latest **51** → application error. Read the long text — application code is telling you what's wrong.
- Latest **64** → ready to post, waiting for the inbound job to fire. Either reprocess via `BD87` or check why the background job didn't pick it up.
- Latest **02** → outbound failed at the partner side. Check `SM58` for tRFC entries — that's usually where the real error is.
- Latest **30** → outbound is queued and waiting. The dispatch background job probably isn't running.

If the long text is cryptic, click "Long Text" — sometimes there's a longer message hidden under the headline.

## Step 3 — look at the data

Open the data records (`EDIDD`). I'm looking for:

- Missing key fields (vendor blank, material blank)
- Wrong values (a tax code that doesn't exist in the receiver, a UoM the receiver doesn't recognise)
- Junk / encoding issues (special characters appearing as garbage)

When in doubt I compare against a known-good IDoc of the same message type. Diffing two IDocs side by side is usually how I spot what's abnormal.

## Step 4 — decide where to fix it

This is the judgement call:

- If the source data is bad → fix in the source system, then re-trigger or resend.
- If config is missing in the target → add the config, transport, then reprocess.
- If partner profile is mismatched → update both ends, transport, then reprocess.
- If structure is wrong (segments missing or extra) → coordinate with the technical team. May need an extension fix and ABAP change.
- If it's a transient tRFC error → restart the failed entry in `SM58`.

The temptation is always to reprocess fast and close the ticket. I try to resist it — fix at the right layer and the same ticket won't come back next week.

## Step 5 — reprocess

For inbound stuck at 51 or 64:
- `BD87` → enter the IDoc number → process. If the fix is right, status moves to 53.

For outbound:
- `BD87` will dispatch a stuck outbound.
- If already dispatched and rejected, you usually need to trigger a fresh IDoc from the source transaction. The original is essentially burnt.

`WE19` is the under-used hero here. It lets you take a copy of an IDoc, edit a field or two, and process it. Great for "if I fix this field, does it post?" without touching the real one.

## Step 6 — record the cause

If it's an ITIL ticket, don't close at "reprocessed, done". Write down:
- What the actual cause was — data, config, structure, partner profile, transport
- Whether other IDocs are likely to be affected
- Whether this is a candidate for a problem record

If I'd skipped this step earlier in my career I would have been firefighting forever. Recording causes is how problem records get raised, and problem records are how recurring incidents stop being recurring.

## Some queries that earn their keep

In `SE16N` for `EDIDC`:
- `STATUS = 51` and `MESTYP = ORDERS` and `CREDAT` in the last 7 days
- Gives a quick view of all failed inbound ORDERS for the week — useful for batching tickets.

In `SE16N` for `EDIDS`:
- Filter by `STAMQU = SAP` and `STATUS = 51`, then look at the `STATXT` field
- Grouping by `STATXT` shows whether the same root message is causing many failures — strong signal it's systemic, not random.

## My honest opinion

- The hardest part of IDoc work is not the technical steps. It's the discipline of fixing at the right layer instead of reprocessing fast.
- Most "broken IDoc" investigations are really data investigations in disguise.
- Set up a daily report of IDocs in error status older than 4 hours. That filter cuts the noise from in-flight messages and surfaces the real failures.
