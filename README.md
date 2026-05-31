# SAP MM / S/4HANA Notes

My personal SAP knowledge base. I'm Geetha — SAP MM functional consultant, currently based in Melbourne. Around 8 years on the job across three employers.

I started writing these notes mainly for myself, while moving between projects. Each time I joined a new engagement I noticed I was relearning the same things — release strategy quirks, IDoc reprocessing steps, the way the Business Partner model changed in S/4HANA. So I started keeping a folder of cheatsheets and topic summaries. This repository is a cleaned-up version of that folder.

Nothing here is theory I picked from a book. Most of it is what I actually do in practice, with some background I added so it stands on its own. Wherever I have a strong opinion, I say so. Wherever I'm unsure, I say that too.

## A bit about my work

- **Capgemini India** (Mar 2022 — Apr 2025): Consultant on the ALDI AHEAD programme (ADM). SAP MM and SAP Retail. Lots of MDG / IDoc work, change requests, problem tickets.
- **Accenture India** (Apr 2021 — Mar 2022): Application Development Senior Analyst on Telia IDS — pure AMS, SAP MM support side of things.
- **IBM India** (Jul 2016 — Apr 2021): Started here. Shell Oil support and then OMV Oil & Gas — the OMV piece was an S/4HANA Bluefield implementation. That's where I learnt most of what I know about cutover and data migration.

I'm in Melbourne now, working through ITIL 4 and refreshing my S/4HANA Sourcing & Procurement reading.

## What's in here

Folder structure is grouped by topic, not by project:

- **01-procurement-p2p** — the bread and butter
- **02-master-data** — material, vendor (now Business Partner), article
- **03-integration-idoc** — ALE / IDoc, mostly troubleshooting flavoured
- **04-testing-quality** — my test script template + how I run defect triage
- **05-s4hana-migration-notes** — Bluefield / Greenfield / Brownfield (the OMV experience shows up here)
- **06-itil-service-management** — incident / problem / change basics
- **07-learning-log** — courses I've finished and what's next

Some files are detailed, some are short. I add to them as I have time.

## Disclaimer

Everything is generic — no client names beyond the employer (IBM / Accenture / Capgemini), no real configuration values, no master data. Just patterns and concepts I have come across in my work. If anything looks like it shouldn't be public, please flag it.

Licence is MIT.

— Geetha
