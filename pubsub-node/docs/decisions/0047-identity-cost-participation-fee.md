# ADR 0047: Identity cost — reclaimable deposit plus a per-epoch, per-topic participation fee

**Status**: Proposed
**Date**: 2026-11-25
**Feature**: identity/Sybil cost, CIP-0201 "Remaining design choices — Deposit
decay" and the deployment-profile open question (review: "how large a deposit").

## Context

The deposit is the protocol's Sybil brake, but a pure locked deposit has two
structural gaps: it is a **stock** cost (an attacker who affords the lockup once
holds identities indefinitely) and it is denominated in a floating asset (ADA
price drift moves the effective μ). The reader review asked for the deposit size
that ties 20 % adversarial participation to an actual cost. Pure decay and
slashing were parked as beyond v1; this defines a simpler mechanism that closes
the stock-cost gap and makes the cost dynamic without an oracle or a
participation proof. ADR 0046 recorded "no decay"; this refines that to "no
unscheduled decay, no slashing" — the draw defined here is the single scheduled
form of balance erosion in v1.

## Decision

**A registered identity pays a small participation fee `f` per dissemination
epoch, per topic it is subscribed to, drawn lazily from its reclaimable deposit.**

- Eligibility at the registration cutoff subtracts `f × elapsed_epochs × |topics|`
  from the entry's deposit. A node left with a non-positive balance is ineligible
  from that cutoff — drained, not deleted — and the entry is reclaimable through
  the existing claim path (returning the remaining balance).
- `f` is a value in the parameter output, set and adjusted by the authorised
  credential (ADR 0045): the dynamic identity-cost knob.
- The fee is **per topic, not per identity**. The gate, the topology and the
  adversarial fraction μ are all per-topic quantities, so the cost of holding an
  attack position must scale with the number of topics an identity is subscribed
  to; a flat per-identity fee would make multi-topic reach free.
- `f` is a **single network-wide rate** (one value in the parameter output), not
  a per-topic-owner rate. Charging per subscribed topic must not hand each topic
  its own fee, or it re-opens the authority question ADR 0045 settled.
- The deposit remains the reclaimable bond (ADR 0046); this is a scheduled
  participation charge, not an idle bleed and not slashing.

## Consequences

- Identity cost becomes a **flow**, not a stock: an adversary sustains a
  continuing payment for every identity on every topic it targets — the quantity
  the resource model prices, and a far better-posed answer to "how large a
  deposit" than a single lockup figure. Unsurprisingly this is exactly the
  "participation cost" the CPS lists as an open question.
- No oracle, no participation proof, no slashing adjudication: the draw is a
  pure arithmetic rule over public fields (`deposit`, `topics`, elapsed epochs,
  `f`).
- On-chain accounting shares the slot↔epoch mapping with the claim rule
  (dissemination epochs are not ledger epochs); both are resolved together.

## Why per-topic (across the CPS personas)

- **Economically — price the attack surface.** Sybil cost should track what an
  identity can *do*, and an identity can do one thing per topic: sit in that
  topic's gate-survivor pool. Subscribing the same identity to a second topic is
  a second unit of attack surface; charging per identity makes that marginal
  unit free, charging per topic makes it cost-proportional.
- **As personas — the multi-topic node is the high-blast-radius one.** An SPO
  (incident channel, own announcements) holds one or two topics, and a dApp or
  governance publisher holds one: for these the per-topic fee is essentially the
  flat fee. Only the aggregator persona — a wallet backend relaying many dApp
  notification streams at once — subscribes broadly, and that is exactly the
  node whose compromise silences the most streams at once. A fee ∝ subscription
  footprint prices the node by its aggregation value, which is also its
  compromise blast radius.
- **As rational choice — keep Sybil self-limiting.** A per-identity fee rewards
  the adversary for over-subscription: register once, subscribe everywhere, hold
  forever. A per-topic fee makes over-subscription costly, so the adversary
  subscribes only where it attacks — and (small-)topic populations stay priced
  apart from large ones, which is also what the CPS "small topics" question wants.
- **As complexity — near-zero added cost.** The registry datum already carries
  `topics: [+ topic_id]`; the draw `f × elapsed × |topics|` is arithmetic over
  fields the validator reads anyway. The one rule this must not drag in is
  per-topic fee setting, hence the single network-wide rate above.

## Alternatives considered

- *Flat per-identity fee*: simpler, but decouples cost from attack surface — an
  identity subscribed to every topic pays the same as one subscribed to a single
  topic.
- *Oracle-anchored deposit*: stabilises the ADA cost, adds an oracle failpoint.
- *Difficulty-adjusted deposit*: self-tunes by registration count, at the price
  of a feedback loop and retroactive unfairness.
- *Pure decay / slashing*: targets participation directly but needs proofs or
  adjudication to be fair.
