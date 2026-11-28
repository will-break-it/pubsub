# ADR 0048: On-chain time — the dissemination epoch as a slot schedule

**Status**: Proposed
**Date**: 2026-11-25
**Feature**: on-chain rules for the node registry (claim, and the participation
fee of ADR 0047). Resolves the parameter output's "epoch origin and a persistent
schedule anchor remain to be specified".

## Context

Dissemination epochs are a protocol concept, not a ledger concept: a validator
sees only slots and a transaction's validity range. Two on-chain rules need the
epoch expressed as a slot range — the deposit claim (the withdrawal delay *D*
and the last required participation epoch) and the participation-fee draw. The
parameter-output schema concedes the epoch origin and schedule anchor are open;
this closes them, once, for both consumers.

## Decision

**Epoch `n` is the slot interval `[origin + s(n), origin + s(n+1))`, where
`origin` is a single fixed slot committed at deployment and `s` is the
cumulative slot offset of a piecewise-constant epoch schedule read from the
parameter output.**

- `origin` is immutable after deployment. It anchors epoch numbering, so every
  node agrees which epoch a slot belongs to — including across an epoch-length
  change, which shifts `s` but never re-number past epochs.
- The schedule is reconstructible: `s(n)` advances by the epoch length effective
  for epoch `n`, and the pending-change mechanism (announce an epoch ahead,
  promote at the boundary) supplies the effective-from points every node reads.
- On-chain "now" for a spending transaction is its declared validity upper
  bound. The validator computes elapsed epochs from that slot, so an operator
  cannot move the accrual or the claim window by choosing a boundary.
- The node-registry datum records the entry's registration epoch anchor, the
  "now = 0" of its fee arithmetic — the fee draw and the claim rule consume the
  same elapsed-epochs value.

## Consequences

- The claim rule and the fee draw share one epoch→slot arithmetic — the single
  mapping ADR 0047 said both would inherit — and it is a pure function of the
  parameter output and the entry datum.
- An epoch-length change is backward-consistent by construction: the origin and
  the piecewise offset keep every already-numbered epoch at its original slots,
  so an old claim anchor never moves.
- The fee draw no longer needs a side-channel sink: `deposit − f · |topics| ·
  elapsed` is computed at claim time and the draw is enforced lazily, exactly as
  ADR 0047 proposed.

## Open, deferred

- The exact Cardano validity-range encoding of "cannot claim before slot X" is
  an implementation detail for the Aiken validators, not this decision.
- Message-retention participation (whether the deposit also waits through
  retention) remains a separate open choice unaffected by this mapping.