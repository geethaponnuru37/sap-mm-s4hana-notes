# Defect triage — the way I work through them

After several years on AMS teams I've settled into a way of working through defects that's lightweight enough for daily use and disciplined enough to keep ITIL audits happy.

## Severity vs priority

These two get confused a lot. Severity is technical impact — how badly is the system behaving. Priority is business urgency — how badly do we need this resolved. They can differ. A cosmetic issue on the CEO dashboard might be S4 / P1.

Typical severity definitions in my projects:

- **S1** — production critical process down. Response target 30 min, resolution 4 hrs.
- **S2** — significant function broken but workaround exists. Response 2 hrs, resolution 1 day.
- **S3** — minor issue, low impact. Next business day response, 5 days resolution.
- **S4** — cosmetic or documentation. 3 day response, next release for resolution.

## The seven-step pattern

### 1. Triage on arrival

Within the SLA. I read the description fully, look at the screenshots / IDocs / logs, and try to reproduce in QAS or a sandbox.

Then the most important question: *is this actually a defect?* Or is it:

- A user training issue
- A data quality issue
- Working as designed but no longer wanted
- An enhancement disguised as a defect

If it's not a defect, redirect with an explanation. Don't let non-defects clog the defect queue.

### 2. Root-cause analysis

For confirmed defects, dig:

- Application logs (`SLG1`), dumps (`ST22`), system logs (`SM21`)
- Trace the flow, debug if needed (with developer help if it's deep)
- Transport history (`STMS`) — was this introduced by a recent change?
- Master data quality
- SAP notes — sometimes there's a known issue and a fix already documented

Record the cause in the ticket. This is the data that feeds problem management later.

### 3. Design the fix

Config fix: document the change in plain language, identify the customising table and field.

Code fix: write a functional spec for the developer. What's the current behaviour, what should the behaviour be, where in the code the change applies.

Data fix: decide whether the change goes in MDG (and is distributed) or directly in the regional system.

### 4. Build and unit test

Make the change in DEV under a transport. Unit test. Confirm the original symptom is gone.

### 5. Move to QAS, integration test

Release transport to QAS. Re-run the failing scenario. Run a couple of adjacent regression tests in the same area.

### 6. UAT and approval

Hand to business / test analyst. Get sign-off recorded in the ticket. For S1/S2 going to production outside a scheduled window, also need explicit CAB approval.

### 7. Deploy and close

Transport to production through standard change process. Verify in production. Watch the next batch run or two for unexpected side effects. Update the ticket, close.

If the cause hinted at a broader pattern, open a problem record.

## Connection to ITIL

The above maps onto:
- **Incident** — initial defect, any business workaround
- **Problem** — root cause, preventive action
- **Change** — production transport approval
- **Release** — bundling fixes
- **Knowledge** — capturing cause and solution in the team knowledge base

## My observations after years of this

- **Triage is the biggest lever** for keeping an AMS team out of trouble. Lazy triage means real defects sit behind non-defects in the queue.
- **Always capture root cause**, not just the fix. A ticket that says "fixed via TR12345" is useless to the next person who hits a similar issue.
- **Three S2 incidents in a month from the same cause** — that's a problem record waiting to be opened. Don't let it stay as three separate tickets.
- **A problem record that closes without a tangible preventive action didn't really solve anything.** I review my open problem records once a fortnight to push them along.
