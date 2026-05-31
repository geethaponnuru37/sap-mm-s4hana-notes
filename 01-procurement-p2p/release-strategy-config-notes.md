# Release strategy — notes I keep coming back to

Release strategy is the approval workflow on PRs and POs. It uses classification (CL02 / CT04) to match a document against a set of conditions, and once matched, the document is "blocked" until the assigned approvers release it.

This is one of the most commonly changed areas in any AMS engagement. Every restructure, every spend-policy update, every new spend category seems to trigger a release-strategy change.

## The building blocks

There are five things to set up:

1. **Release groups** (PRs only) — way to group strategies for the worklist.
2. **Release codes** — identify the approver role. E.g. 01 = Department head, 02 = Plant manager.
3. **Release indicators** — what status the document moves to after each release. "Blocked", "Released for RFQ", "Released for PO creation", "Fully released".
4. **Release strategies** — the actual workflow definition. Which codes apply, in what order, which indicator each step sets.
5. **Classification (CL02 / CT04)** — the matching logic. Characteristics like total value, plant, purchasing org, doc type, material group.

Configuration sits under SPRO → MM → Purchasing → Purchase Requisition (or PO) → Release Procedure.

## How I configure one end-to-end

The order matters or you end up redoing things:

1. Create characteristics in `CT04`. Each characteristic must reference a real table field — `CEBAN-GSWRT` for PR total value, `CEKKO-BSART` for PO doc type, and so on.
2. Create a class in `CL02` of class type 032 (PR) or 032 (PO header — same number but different class type internally). Add the characteristics to it.
3. Set up release groups, codes, indicators in SPRO.
4. Create the release strategy itself. Assign the class.
5. Maintain the actual classification values — "if total value > 50000 AND plant in (1000, 2000), use this strategy".
6. Save, transport, test.

## Where people get stuck

I've debugged enough release strategy problems to have a mental top-5:

1. **Strategy not triggering when it should** — almost always the classification values. Most often a missing `<=` vs `<` boundary or a plant that wasn't added to the list.
2. **Two strategies overlap and the wrong one fires** — both have matching classification. Make them mutually exclusive. Usually means rethinking how values are banded.
3. **User has the release code but can't release** — auth object `M_EINK_FRG` (PO) or `M_BANF_FRG` (PR) isn't in their role.
4. **PR releases fine but conversion to PO is still blocked** — release indicator "Changeability" setting on the PR strategy is locking the conversion. Adjustable in the indicator config.
5. **Multi-currency value bands behaving oddly** — strategy is reading group currency but business expects local currency, or the other way round. Decide which one and stick to it.

## CL30N is my friend

This is the test transaction for classification. You feed in the class and the field values, and it tells you which strategy would match. I run this before transporting any strategy change, to be sure I haven't accidentally broken an existing strategy by adding a new one.

In the displayed PR / PO, the menu has a "Release Strategy" option that shows which strategy is currently applied, which codes have released, and what's still pending. Useful when investigating "why is this PR still blocked".

## My opinions

- For simple environments, one strategy with two or three codes is enough. Don't over-engineer.
- For multi-country setups, value bands should be in the local company-code currency, not the group currency. FX moves would otherwise cause documents to flip strategies for no business reason.
- Always document the strategy logic as a plain-language matrix and circulate to the business. The SPRO screens are unreadable for non-SAP people, and you'll spend half your time explaining what they configured.
- If a strategy has more than four release codes, take that as a signal to question the business process — usually means too many sign-offs and a slow procurement cycle. Sometimes a chat with the head of procurement saves a lot of configuration work.
