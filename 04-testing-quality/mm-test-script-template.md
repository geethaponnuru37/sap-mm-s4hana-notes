# MM test script — my reusable template

A skeleton I use whenever I have to write a test script for an MM change. I have a copy of this in Word as well, with screenshots-as-evidence sections. The structure below is what works for me — feel free to adapt.

## Header

- Test script ID
- Title (e.g. *Create standard PO with release strategy*)
- Author
- Version, date
- Module (SAP MM, possibly + FI for the postings)
- Related requirement / change / JIRA ID / ServiceNow change number
- Test type — Unit / Integration / Regression / UAT
- Test data prerequisites — list of materials, vendors, plants, users
- Test environment — system + client (e.g. D4S / 200)

## Test scenario summary

One short paragraph. *What this test proves, and why it matters to the business.*

I find this is the section testers most often skip and reviewers most often miss. It's the only place that tells future-me why this test exists.

## Pre-conditions

The state the system needs to be in before the test will run cleanly. For an MM test that usually means:

1. Material X exists in plant Y with all relevant views (Basic Data, Purchasing, Accounting at minimum).
2. Vendor exists in the company code with the supplier role.
3. Purchase organisation is assigned to the plant.
4. Release strategy is active and the tester's user has the right release code.
5. Any specific test data values (info record price, etc.) maintained.

I always list these explicitly even if "obvious". Half the failed test executions I've seen were because a precondition wasn't met.

## Test steps

Numbered steps, with action / expected result / actual result / status columns.

For a typical PO + GR + IR scenario:

1. Log in as PR-creating user, open `ME21N`.
2. Enter vendor, purchasing org, purchase group.
3. Enter material, plant, quantity, delivery date — confirm net price defaults from info record, tax code defaults correctly.
4. Save the PO. Note PO number.
5. In `ME23N` confirm the release strategy is applied and the right code is pending.
6. Log in as approver, open `ME29N`. Release using the assigned code.
7. Confirm PO status moves to fully released.
8. Post goods receipt via `MIGO` mvt 101 for full quantity. Confirm material document posts.
9. Post invoice in `MIRO` matching PO + GR. Confirm three-way match passes and FI document is created.

Expected vs actual goes in adjacent columns. The "Status" column is where the tester marks Pass / Fail / Re-test.

## Post-conditions

What the system should look like after the test passes:
- PO in status fully released, fully received, fully invoiced
- Stock has moved as expected
- FI postings made (GR/IR clearing, vendor liability, etc.)
- No errors in `SLG1`

## Pass / fail criteria

I keep this strict. The test passes when **all** the steps match their expected results. One step deviating is a fail. Anything less rigorous and you end up with "mostly passing" tests that mask defects.

## Defect handling

For any failure:
1. Capture screenshot.
2. Record system, client, user, time, transaction.
3. Log defect in the tool with severity per the team matrix.
4. Link defect to this script and to the originating change request.
5. Re-run the test after the fix; update the Status column.

## Sign-off

A small table at the end with rows for tester, reviewer, and (for UAT) business sign-off.

## What I do in practice

- **Unit testing** in DEV/QAS — I run the script myself before handing it over.
- **Integration testing** — I add the adjacent module steps so the chain is covered, not just MM in isolation. The most valuable integration tests are the ones that catch MM-to-FI account determination problems.
- **Regression** — I keep a library of these scripts and re-run high-risk ones for every support pack or major release.
- **UAT** — I simplify the script for business users. Hide the transaction codes behind action descriptions, embed screenshots, give them a "what success looks like" picture.

## One thing I'd tell a junior consultant

Write the test script before you start configuring, not after. The act of describing the expected behaviour usually surfaces edge cases in the requirements that nobody thought about. I've saved myself rework many times this way.
