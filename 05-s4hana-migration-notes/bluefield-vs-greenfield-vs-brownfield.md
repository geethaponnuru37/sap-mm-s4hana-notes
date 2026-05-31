# Bluefield, Greenfield, Brownfield

Three approaches to getting from ECC to S/4HANA, each with very different cost / risk / time profiles. Choosing the wrong one is one of the most common reasons an S/4HANA programme overruns or doesn't deliver the value the business expected. Anyone working at the boundary between business needs and SAP delivery needs to be able to explain the trade-offs in plain language.

I worked on a Bluefield-style S/4HANA programme at IBM for an energy sector client. What I write below is partly textbook, partly from that experience.

## Greenfield — start fresh

Build a brand new S/4HANA system. Load only master data and select open transactional data going forward. Treat the old ECC as a read-only archive.

When it works: when the business actually wants a process transformation, when the legacy is too customised to carry forward, when there's appetite to adopt standard SAP best practices.

Pros: cleanest end state, no legacy baggage in customising or code, best chance to use S/4HANA-native features (Fiori, embedded analytics, Business Partner), easier to scope.

Cons: longest project, highest cost, biggest change-management load on business users, historical data only accessible via legacy or separate archive.

Typical duration: 12–24 months for a mid-size enterprise.

## Brownfield — convert in place

The existing ECC system is upgraded in place using the Software Update Manager with Database Migration Option (DMO). Custom code, configuration, and all data come along.

When it works: when ECC is already in decent shape, when the business doesn't want a transformation, when the primary goal is technical modernisation.

Pros: shortest duration, least business disruption, existing reports and integrations largely keep working.

Cons: all the legacy debt comes with you. Every dead custom object, every unused config, every obsolete master record. Plus the mandatory S/4HANA simplifications still need to be addressed (Business Partner conversion is unavoidable; material number length, financial document changes, the Universal Journal).

Typical duration: 6–12 months for mid-size.

## Bluefield — selective data transition

Stand up a clean S/4HANA system, then move *selected* config, master data and transactional data from the old ECC. Decide per object what comes across.

When it works: when the business wants a cleaner end state than Brownfield but cannot afford to lose years of open contracts and procurement history, and cannot stomach the duration of full Greenfield. Common in large complex enterprises with multi-year transactional histories. This was OMV's situation.

Pros: cleaner customising than Brownfield, less data baggage, opportunity to redesign selectively without doing everything at once, some history brought across so business continuity isn't lost.

Cons: more expensive than Brownfield, more complex. Needs specialist tools — SAP DMC + SLT, or third-party tools like SNP CrystalBridge. Cutover is more complex because source and target are different systems.

Typical duration: 9–18 months.

## Quick comparison

| Criterion | Greenfield | Brownfield | Bluefield |
|-----------|-----------|------------|-----------|
| Legacy carried forward | Nothing | Everything | Selected |
| Process redesign opportunity | Maximum | Minimum | Targeted |
| Duration | Longest | Shortest | Medium |
| Cost | Highest | Lowest | Medium |
| Change management load | Heaviest | Lightest | Medium |
| Cutover risk | High (big bang) | Medium (technical) | Medium (data + cutover) |

## My energy-sector project experience

Quick summary of why the project took a Bluefield / selective data transition approach:

- The legacy ECC carried years of acquired-company configurations that were no longer used.
- The business wanted to consolidate countries onto a single chart of accounts and a unified enterprise structure. Too disruptive for Brownfield.
- Full Greenfield would have meant losing too many open POs, contracts, and procurement history.

What that looked like in practice:

- Standing up the new S/4HANA system with a designed-for-the-future enterprise structure.
- Defining migration scope per object — vendor master, material master, info records, contracts, open POs and GRs were in scope. Closed historical POs older than 3 years were left behind in the archived ECC.
- Multiple mock load cycles before the real cutover. I think we did three on the MM side, which gave us confidence in the field mappings.
- Coexisting with the legacy ECC for a hypercare period while business teams adjusted to the new system.

The cutover weekend was tense but the planning paid off. Most of the issues that came up in hypercare were master data quality issues, not configuration issues — which I'd say is a sign the planning was right.
