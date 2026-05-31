# SAP MM / S/4HANA Portfolio Notes

My personal SAP knowledge base and portfolio evidence set. I'm Geetha, an SAP MM functional consultant currently based in Melbourne, with around 8 years of hands-on experience across SAP support, implementation, integration, testing, and service management work.

I started writing these notes while moving between projects. Each new engagement brought the same practical patterns back into view: release strategy quirks, IDoc reprocessing steps, master-data distribution failures, testing discipline, and the Business Partner model in S/4HANA. This repository is a cleaned-up version of those notes.

The notes are written in my own words and focus on how I have applied SAP MM/S/4HANA knowledge in real support and delivery situations. They are intended to show practical understanding, troubleshooting approach, and professional currency without exposing client data.

## A bit about my work

- **Capgemini India** (Mar 2022 - Apr 2025): SAP MM / SAP Retail consultant on a large European retail SAP programme. Focus areas included MDG, IDoc monitoring, change requests, incident/problem analysis, and production support.
- **Accenture India** (Apr 2021 - Mar 2022): Application Development Senior Analyst on an AMS engagement for a telecommunications client. Focused on SAP MM support, service procurement, defect handling, and operational stability.
- **IBM India** (Jul 2016 - Apr 2021): SAP support and implementation work across energy sector engagements, including an S/4HANA selective data transition programme. This is where I built most of my cutover, data migration, and integration experience.

I'm in Melbourne now, working through ITIL 4 and refreshing my S/4HANA Sourcing & Procurement reading.

## ACS evidence focus

For ACS purposes, this repository demonstrates:

- **SAP functional analysis**: P2P process mapping, purchase document types, release strategy, master-data dependencies, and MM/FI integration points.
- **System integration and troubleshooting**: ALE/IDoc fundamentals, partner profile checks, status-code diagnosis, and reprocessing discipline.
- **Testing and quality assurance**: reusable MM test-script structure, defect triage, root-cause recording, UAT support, and regression thinking.
- **Service management**: incident, problem, and change separation using ITIL-aligned practices.
- **Professional development**: ongoing learning log covering SAP MM, S/4HANA, MDG, integration, and ITIL topics.

## What's in here

Folder structure is grouped by topic, not by project:

- **01-procurement-p2p** — core purchasing and approval concepts
- **02-master-data** — material, vendor (now Business Partner), article
- **03-integration-idoc** — ALE / IDoc, mostly troubleshooting flavoured
- **04-testing-quality** — my test script template + how I run defect triage
- **05-s4hana-migration-notes** — Bluefield / Greenfield / Brownfield
- **06-itil-service-management** — incident / problem / change basics
- **07-learning-log** — courses I've finished and what's next

Some files are detailed, some are short. I add to them as I have time.

## Disclaimer

Everything here is generic. There are no client master-data records, real configuration values, transport IDs, ticket IDs, screenshots, system names, or confidential process details. Employer names are included for career context; end-client details have been anonymised for public sharing.

Licence is MIT.

Geetha
