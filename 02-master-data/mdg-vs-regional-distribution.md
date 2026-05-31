# MDG and the regional SAP systems

In multi-region SAP landscapes — most large retailers, most global manufacturers — master data is usually governed centrally and then distributed out to the regional systems that run the local operations. SAP Master Data Governance (MDG) is the SAP product that does the governance bit. The distribution is just standard ALE / IDoc on top of it.

On ALDI AHEAD I spent three years in this architecture, with the central S/4HANA Retail / MDG hub feeding the regional SAP instances per country. Most of what I know about it is from that.

## The picture

The hub holds the *governed* master data — articles, business partners, pricing, contracts. Changes go through MDG workflows (create / change / block requests), get approved, then "activate" and distribute outbound via IDocs to each subscribed regional system.

```
                       MDG hub
                          |
                          | IDoc distribution
                          |
        +--------+--------+---------+--------+
        |        |                  |        |
     Region A  Region B          Region C   Region D
     SAP       SAP                SAP        SAP
```

It's not glamorous architecture but it works, when configured carefully.

## The objects usually governed centrally

- Business Partner (vendor / customer) — name, address, bank data, tax IDs, payment terms
- Material / Article — descriptions, units of measure, hierarchies
- Pricing — conditions, scales, contracts (in retail this is huge)
- Finance master — chart of accounts, cost centres, profit centres
- Custom domains the implementation team defines

## How the distribution mechanics work

- Outbound is event-driven. Activate a BP in MDG → outbound IDoc is built using the distribution model and partner profile → sent to each downstream system in the model.
- Inbound at the regional side: partner profile defines the inbound function module, which applies the change. IDoc status moves from 50 to 53 (success) or 51 (application error).
- Acknowledgements come back via ALEAUD audit IDocs, if you've set them up. Strongly recommend setting them up — otherwise MDG can mark a distribution successful while the regional posting silently failed.

## The classic failure modes

After a few months on ALDI I could almost call which type of failure was waiting in the morning ticket queue. The recurring patterns:

1. **Partner profile drift** — MDG's outbound profile carries a segment the regional inbound profile doesn't know about. IDoc fails with a structure error. Fix is a coordinated transport on both sides.
2. **Code value mismatch** — a new payment term added centrally but not yet maintained in the regional code list. Inbound fails with "value not allowed". Fix in regional, then re-distribute.
3. **Manual change in the regional system overwritten by next distribution** — someone fixes a vendor record directly in Region A's SAP, but MDG distributes again and silently overwrites it. This is governance failing, not technology failing.
4. **Acknowledgement gaps** — if ALEAUD isn't set up, you have to manually reconcile what made it through.

## What I actually do day-to-day in this kind of architecture

- Monitor outbound from MDG via `WE05` filtered by the relevant message types.
- For each failing IDoc, work out whether the cause is in MDG source data, the partner profile, or the regional system. Fix at the right layer — don't paper over.
- Reconcile regional master data against MDG on a regular cadence — a custom report that compares key fields. We had this running weekly.
- Treat recurring failures as a problem record in the ITIL sense, not a series of one-off incidents. Driving the underlying fix is the only way to stop seeing the same ticket every fortnight.

## Things I wish I'd understood sooner

- Governance is only as strong as the validation rules in MDG. If they're weak, the workflow approvals become rubber-stamps and bad data flows downstream anyway.
- The regional inbound IDoc handler should have defensive checks. Trusting MDG completely is fine in theory, fragile in practice.
- **Domain ownership** matters more than people think. Who owns article master globally, who owns regional pricing, who owns vendor bank data — most operational disputes come from this being unclear.
