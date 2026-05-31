# Incident, problem, change

Three different ITIL processes that newcomers to AMS confuse all the time. They sound similar but mean very different things and need to be kept apart for the team to actually function. I've spent 3+ years living inside this discipline at Capgemini and it's one of the most important things I've internalised about service management.

## The definitions

**Incident** — an unplanned interruption to a service, or a reduction in quality. The user can't post a goods receipt. An IDoc is failing. A batch job has stopped. Incident management is about *restoring service* fast. Workaround or permanent fix doesn't matter at the incident layer — speed of restoration does.

**Problem** — a cause, or potential cause, of one or more incidents. The pricing IDocs from MDG to country X keep failing every fortnight. That's a problem. Problem management is about *finding and removing the cause* so the incidents stop recurring. A problem record might take weeks or months to close — that's fine because it's not about real-time restoration.

**Change** — the addition, modification or removal of anything that could affect a service. A config update, a release, a transport into production. Change management is about *assessing risk, getting approval, scheduling, and verifying*.

## A worked example

This is roughly the kind of pattern I'd see on a typical week:

- Day 1, 10am: user reports an IDoc failure to country DE. → **Incident** ticket. I reprocess after a manual master-data fix. Closed by midday.
- Day 3: same user reports another IDoc failure to DE. → New **incident**. Same workaround applied.
- Day 5: third occurrence in a week. Now I see a pattern.
- Day 5 onwards: **Problem record** opened, linking the three incidents. Investigation starts.
- Day 12: cause identified — MDG isn't propagating a specific tax code to DE. Solution designed.
- Day 14: solution proposed, raised as an RFC, **change ticket** created. CAB approves.
- Day 16, Saturday change window: transport deployed.
- Day 30: no further occurrences. **Problem closed.**

Same underlying business issue, three different ITIL processes, clear hand-offs. That's how it's supposed to work.

## How people get this wrong

The anti-patterns I've watched (and at the start of my career, contributed to):

1. **Recording every recurrence as a fresh incident** and never opening a problem record. Team becomes a treadmill. Same workaround applied again and again. Nobody ever drives the cure.
2. **Pushing config changes through as incident fixes.** This bypasses change management — no risk assessment, no CAB, no rollback plan. It works until something breaks in production at 2am.
3. **Confusing a problem record with an enhancement request.** A problem removes a cause of unwanted behaviour. An enhancement adds new desired behaviour. They follow completely different workflows.
4. **Closing incidents without recording the cause** when the cause was known. This is the data loss that makes pattern recognition impossible later.

## Things I've learnt

- **Decide deliberately when to escalate an incident into a problem.** Not every incident needs a problem record. My rule of thumb: more than 2 occurrences in 30 days from the same root cause, OR any S1 incident regardless of frequency. Anything else is just normal incident work.
- **Change discipline protects production.** Every transport that bypasses CAB is a potential future incident. I've lost count of how many "urgent untracked changes" have turned into outages.
- **A problem record without a tangible preventive action is just paperwork.** Config change, code fix, monitoring rule, runbook addition — something concrete needs to come out the other end. Otherwise the team will just close the problem and the incidents will resume.

## How it shows up in tooling

In every AMS project I've been on:
- ServiceNow has separate modules for Incident, Problem, Change, Release, Knowledge — each with its own SLA and workflow.
- JIRA usually sits next to ServiceNow for change-request / enhancement delivery.
- Linkage matters: incidents link to the problem record, problem links to the change record, change links to the transport. Without those links, audit traceability falls apart.
