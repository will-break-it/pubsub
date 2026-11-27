# ADR 0045: Parameter authority — an authorised credential for v1

**Status**: Proposed
**Date**: 2026-11-25
**Feature**: the on-chain parameter output (deployment record), CIP-0201
"Remaining design choices — Authority over the parameter output".

## Context

The parameter output is the one shared record whose value every node reads and
whose change changes what every node computes (epoch length, pending schedule).
CIP-0201 lists five candidate arrangements for authority over it: (1) no output
at all — config plus a coordinated restart; (2) an immutable output; (3) a
Cardano governance action; (4) an authorised credential named in the output;
(5) per-topic, set by the topic's owner. The current CDDL schema supports (2)
and (4) directly; (1), (3) and (5) require revisiting the deployment model.

## Decision

**v1 fixes the parameter output to carry an authorised credential
(arrangement 4), with the immutable output as the documented no-authority
alternative.**

- The datum's `authority` arm `[1, credential]` is the reference form. Whoever
  deploys the output names one credential; the redeemer arms (announce a
  change, cancel a pending change, promote a pending change once its epoch has
  arrived) are the only spend paths.
- The immutable arm `[0]` stays in the schema for deployments that want no
  standing authority; a change is then a new deployment nodes migrate to.
- The pending-change mechanism is the bounded way a change takes effect: the
  new epoch length is announced one epoch ahead and promoted at the boundary.

## Consequences

- One party can move the epoch length. The value is public and every effect is
  recomputable and auditable, so the authority is bounded but real — stated as
  such in the CIP, not hidden.
- A v1 validator enforces exactly the three redeemer modes and nothing else; it
  never computes the sizing assumptions, which remain node configuration.

## Alternatives considered

- *No output / config only*: simplest, and the only arrangement with no standing
  authority, at the cost of making every change a coordinated restart and
  removing an on-chain schedule a node can read.
- *Governance*: removes the single party, at the cost of routing the smallest
  change through a governance cycle.
- *Per-topic*: gives each topic its own cadence, at the cost of one beacon and
  one snapshot per topic and restating δ and p per topic.
