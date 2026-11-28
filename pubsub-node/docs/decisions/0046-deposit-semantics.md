# ADR 0046: Deposit semantics — reclaimable registration deposit, no decay, no slashing in v1

**Status**: Proposed
**Date**: 2026-11-25
**Feature**: the node registry deposit, CIP-0201 "Remaining design choices —
Deposit decay".

## Context

The deposit is the protocol's Sybil resistance: one identity costs locked ada
for as long as its registration stands. CIP-0201 lists "deposit decay" as a
remaining design choice. The reader-review session (logbook 2026-09-01) walked
through decaying deposits, continuous stake redistribution and
threshold-signature participation proofs and parked all three as beyond the
proposal.

## Decision

**v1 deposit is a registration deposit: reclaimable, with no decay and no
slashing.**

- A node's entry locks `deposit` coin while the entry stands. It is returned
  once the claim rule is satisfied — after the withdrawal delay and the last
  required participation epoch — through the entry's `claim` redeemer.
- Decay (value bleeding over time), slashing (loss for provable misbehaviour)
  and participation proofs are named post-v1 mechanisms, not v1 obligations.

## Consequences

- The cost of an identity is the time-value of the locked deposit plus the cost
  of running the identities — exactly what the resource model prices.
- Rational silence and the free-riding equilibrium fold into the adversarial
  fraction μ rather than pretending to be detectable; the CIP states this
  plainly as an assumption, not a mechanism.
