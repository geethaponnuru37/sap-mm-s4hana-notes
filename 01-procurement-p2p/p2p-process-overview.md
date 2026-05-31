# Procure to Pay — the basic flow

Procure to Pay (P2P) is the most-touched process in any SAP MM job I've had. Every defect ticket, every change request, every implementation has at least some of it. So I want to keep a clean reference of the flow here, partly for myself when I forget the small details, partly because new joiners on AMS teams always ask the same questions.

## The seven steps

1. **Purchase requisition (PR)** — internal request to buy something. `ME51N` to create, `ME52N` to change.
2. **Source determination** — which vendor will supply, and from which contract or info record. Source list (`ME01`) and quota arrangement (`MEQ1`) drive this when configured.
3. **Purchase order (PO)** — formal commitment to the vendor. `ME21N` / `ME22N` / `ME23N`.
4. **Goods receipt (GR)** — warehouse confirms the delivery. `MIGO`, movement type 101.
5. **Invoice receipt (IR)** — finance team posts the vendor's invoice against the PO. `MIRO`.
6. **Three-way match** — the system checks PO vs GR vs IR. If the values and quantities line up within tolerance, invoice posts. Otherwise it blocks for review.
7. **Payment** — finance runs `F110` to pay the vendor and clear the open liability.

These seven cover most situations. Variants exist for stock transport, subcontracting, services and consignment but the spine of the process is the same.

## Variants worth being aware of

- **Stock transport order** — PO is between two of your own plants, not to an external vendor. Movement type 641 (or 641O for one-step). Document type usually UB.
- **Subcontracting** — you supply components to the vendor, who returns the finished product. Item category L.
- **Service procurement** — service entry sheet (`ML81N`) instead of a goods receipt.
- **Third-party** — vendor ships direct to your customer; you never take stock.
- **Consignment** — stock sits in your plant but is owned by the vendor until withdrawn.

In practice, retail engagements use a lot of standard + stock transport. Oil and gas tends to involve more subcontracting and service procurement. Telecom engagements I've supported were heavy on service procurement too.

## Where MM touches other modules

The MM picture is incomplete without remembering it integrates outward.

- **MM → FI**: GR posts the GR/IR clearing entry; IR posts the vendor liability. The account determination is controlled by `OBYC` plus the valuation class on the material master. See the OBYC note in this folder.
- **MM → SD**: stock transport orders and intercompany billing cross both modules. The PO on one side creates a sales document on the other.
- **MM ↔ external systems**: vendor portals, EDI partners, supplier networks. IDocs of type ORDERS and INVOIC are the standard message types here.

## The defects I most often see

After 8 years I can almost predict where tickets will come from. The top three:

1. **GR/IR mismatch** — almost always a unit-of-measure inconsistency between the PO and the GR. The vendor delivered in one UoM, the GR was posted in another, and the conversion factor wasn't maintained.
2. **Account determination error at GR** — missing valuation class, missing entry in OBYC, or a transaction key that's not assigned. Usually fixed in 15 minutes once you know where to look.
3. **Release strategy not triggering or triggering wrong strategy** — see the separate release strategy note. This is the most common config-change request too.

The 4th and 5th would be **info record price not pulled** (info record missing or expired) and **tax code defaulting wrong** (tax determination configuration in the material/vendor combination).

## My one piece of advice for newcomers

Get to the point where you can draw the P2P flow on a whiteboard from memory, with the SAP transactions and the resulting documents and the FI side-effects. Once that picture is in your head, every defect ticket becomes "which arrow on the diagram is broken" — which is a far more productive question than "what is this error message".
