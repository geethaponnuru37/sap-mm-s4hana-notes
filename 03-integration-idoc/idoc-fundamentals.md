# IDoc basics

IDoc — Intermediate Document — is the standard format SAP uses for moving structured business data between systems. Under ALE for SAP-to-SAP, under EDI for SAP-to-external partners. It's been around for ever and is still the backbone of most integration I've worked on.

## What an IDoc actually is

Three parts:

- **Control record** (table `EDIDC`) — the header. Message type, basic type, sender, receiver, status, direction (outbound = 1, inbound = 2), timestamp.
- **Data records** (`EDIDD`, stored physically in `EDID4` for active, `EDID2` for archived) — the actual payload, organised into named segments.
- **Status records** (`EDIDS`) — the audit trail. Every processing step appends a row here.

When I'm investigating any IDoc problem, I always start at the control record. Direction, status, message type, sender, receiver — those five things route the rest of the analysis.

## Message type vs basic type vs extension

This trio takes a minute to internalise but worth getting right.

- **Message type** is the business meaning. `ORDERS` is "a purchase order".
- **Basic type** is the structural template. `ORDERS05` is version 5 of the ORDERS structure.
- **Extension** is a custom add-on. Something like `ZORDERS05_EXT` adds project-specific segments on top of the standard.

The combination (message type + basic type + extension) defines exactly what segments and fields are valid. The partner profile maps each combination to a process code on the inbound side or an output mechanism on the outbound side.

## ALE distribution model

For SAP-to-SAP, the distribution model in `BD64` says who sends what to whom. It lists logical systems, the message types flowing between them, and any field-level filters. Once configured, the model is distributed across all participating systems so they all agree on the topology.

## Partner profile

Per partner — logical system or external — `WE20` defines the inbound and outbound parameters per message type. For outbound: process code, basic type, extension, recipient port, output mode. For inbound: process code, syntax check option, post mode.

Mismatched partner profiles between sender and receiver are the most common root cause of distribution failure I've seen.

## Ports

`WE21` defines the technical channel — file, RFC, ABAP-PI, XML HTTP. The partner profile points at a port, the port at the actual technical destination.

## What happens on outbound

A business event happens — a PO is released, a master record is activated. An output record (NAST) is created. The output processing program writes the IDoc with header and data, sets status to 03 (dispatched), and hands it to the port. Once acknowledged it can move to status 12. Failures end up in 02, 04, or 05 depending on where in the flow it went wrong.

## What happens on inbound

The IDoc lands at the port. Status 50 (added). Syntax / structure check — fail goes to 51, success to 64. The process code calls the inbound function module which applies the change to the application. Success is 53, application error is 51.

## Transactions I use most

- `WE02` — IDoc list, the everyday investigation transaction
- `WE05` — similar but with more selection options
- `WE19` — IDoc test tool. Lets you edit a copy and re-process to test a fix
- `BD87` — reprocess inbound, dispatch outbound
- `WE20` — partner profile
- `WE21` — ports
- `BD64` — distribution model
- `SM58` — tRFC monitor, when the problem is transport-layer

## A thing that has burnt me

IDocs are not magically idempotent at the application level. Reprocessing a status-53 IDoc can post a duplicate business document if the inbound function module doesn't check. I learnt this the hard way once on a goods movement IDoc — reprocessed it after a "maybe it didn't post" panic and ended up with double stock. Always confirm before mass-reprocessing.

The other one: archive jobs (`RSEXARCA`) silently delete IDoc payloads after retention. If you're investigating a months-old failure and the data records are gone, you can't see what was inside. Capture what you need early.
